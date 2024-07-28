                                                          <h2>ITRODUCTION </h2><br>
 Human posture detection is now widely explored in many application contexts, ranging from
 content-based retrieval, surveillance, indoor and outdoor monitoring, virtual reality until 
animation and entertainment. In particular, we are interested in human posture detection in the
 framework of home-human interface. The scope is twofold. The former is to improve the control
 of electronic parts in the house, in order to aid the inhabitants (especially if disabled) in daily life.
 The latter is to find a way to monitor people health without some invasive approaches often 
proposed in tele-medicine. In this case an intelligent video-based tele-assistence service, for 
remote control of disabled or elderly people, needs advanced computer vision techniques able to
 detect moving objects, classify people, localize them in the environment and detect their posture
 and behavior. A typical application is detecting if the person is walking, sitting or falling down
 and lying on the floor and eventually sending an alarm to a remote human operator. To this aim
 this paper proposes an approach for people posture classification. This topic is very popular in
 this moment and many papers and research works address this human body analysis using only 
information about silhouette and its boundary. They first use hierarchical classification in main
 and secondary postures, processing vertical and horizontal histogram profiles from the body
 silhouette. Then they locate body parts on the silhouette boundary’s corner. Our approach is
 similar to [2], as well as concerning histogram-based features, but differently from it, it is not 
based on a priori defined model. In fact the main strength of our approach is a machine learning 
phase, exploited to create feature models, further used in the classifier.

                                                2. RELATED WORKS
In recent years an increasing number of computer vision projects dealing with detection and
 tracking of human posture have been proposed and developed. Analyzing literature's works, two
 basic approach to the problem stand out. From one side, systems (like Pfinder[5] or W4[3]) use 
a direct approaches, based the analysis on a detailed human body model: an effective example is
 the Cardboard Model[4]. In many of these cases, an incremental predict-update method is used, 
retrieving information from every body part. Nevertheless these systems are generally too 
sensitive, when loosing information about features, needing often a reboot phase. For this 
reason, the segmentation process has to be as precise as possible using specific human cues (eg. 
Skin detection). Often this can contributes to system instability because some of these features 
could not be found in every frame (caused e.g. by overlapping). In order to bypass these 
drawbacks, where no body parts control needed, many researchers deal with the problem in a 
indirect way using less, but more robust, information about the body. Many of these approaches 
are based on human body silhouette analysis. The work of Fujiyoshi et. Al. [1] uses a synthetic 
representation (Star Skeleton) composed by extremal boundary points. In [2] Haritaogluet al. add
 to W4 framework [3] some techniques for human body analysis using only information about
silhouette and its boundary. They first use hierarchical classification in main and secondary 
postures, processing vertical and horizontal histogram profiles from the body silhouette. Then
 they locate body parts on the silhouette boundary’s corner. Our approach is similar to [2], as 
well as concerning histogram-based features, but differently from it, it is not based on a priori
 defined model. In fact the main strength of our approach is a machine learning phase, exploited 
to create feature models, further used in the classifier.



                          3. ASSUMPTIONS AND CONSTRAINTS
The approach aims to define a framework for posture classification over previously detected 
people. Thus the part of moving object detection and people classification is not the strength of 
the paper.In order to be as flexible as possible, we constrain the problem dimensionality to the 
2D case, reducing the analysis to monocular images. This allows our application to be applied 
also in those situations where a 3D reconstruction can’t be issued, either for environmental or 
computational limits. Therefore we suppose to have a point of view where the human posture 
can be easily perceived without ambiguities also in the image plane. We consider images from
 indoor environment with an unknown but fixed camera position. The method can be exploited 
in outdoor environment too but we exclude high luminance variation and other artifacts that 
could add difficulties in moving object detection. Moreover, one people at time is considered: in
 this paper we don’t address problems of people overlapping, neither problems of human parts 
hiding, problems that we could suppose solved by a very precise moving object detection and 
tracking systems or by a multi-camera system.

4. THE PROPOSED APPROACH
A high level system layout is represented in Figure 1,  where gray block indicates parts referred 
to this paper. As previously stated, we assume to have the capability to extract and track human 
bodies from a movie. In our tests we exploit a system called Sakbot (Statistical & Knowledge-
Based Object Detection) [6] able to detect and track moving objects (called MVOs, Moving 
Visual Objects). The basic process is based on a adaptive background suppression, where 
background is modelled using statistical and knowledge-based data. Sakbot can extract 
foreground objects, distinguish real MVOs from shadows and other artifacts such as “ghosts” 
(i.e. errors in background modeling), and track MVOs during the time. Every tracked MVO is 
then processed by a pre-classifying phase, in order to distinguish between people and non-
people tracks. For this work, we did not address this problem, using a very simple classifier, based on geometrical features. As in Figure 1, MVOs classified as “People tracks”
are then passed to HPMS (Human Posture Monitoring System) which detects and analyzes the
 posture of the person in order to recognize if dangerous situations occurred (e.g. falling or long 
time inactivity). This part is basically composed by three block. The first, PMFC (Posture 
Model-Free Classifier) is a statistical example-based classifier able to distinguish between four 
main body postures. It uses a human-free model created by a specific training phase. When 
human body posture is recognized, the system pass it to the next block (Head Finder) which 
investigate the silhouette boundary, looking for the top-of-the-head point. This is done using a 
Jarvis-Convex Hull scan and light tracking phase with topological rules. The Head Finder has 
the main goal to make the posture detection more effective and robust, discriminating between 
ambiguous cases. Finally the control switches to the third block (Posture Monitor) which detect 
dangerous situation judging the history of body posture and head position. This is done using a 
finite state chart which models person’s behaviour. The posture monitor generates, if required, 
an alarm as indicated in Figure 0.


                                           4-1. Posture model free classifier
In order to recognize human body posture, we used a similar knowledge classification, proposed
 by [2]. In the matter of fact, we discriminate four postures which represent our classifier’s 
states: standing, crawling, laying and sitting. An algorithm layout is reported in Figure 1. Every
 MVO, classified as a person, is processed extracting information from its silhouette as in [6]. 
Let B a cloud of 2D points, we compute the cardinality ( indicated with #) of horizontal and 
vertical projections respectively π and θ as follow:
θ () #( , ) | x = ∈= { x y Bx x pp p } (1)
π () #( , ) | y x y By y = ∈= { pp p } (2)
These two integer functions represent the basic classifier features. Although other features have 
been explored and could be added to the classifiers, in our tests, they have been proved to be
 reliable enough. The classifier works in two different modalities: training and run-time. In the
 first phase we construct two probability maps for every posture (state), using the respective 
silhouette projections. problem is how to compare an 1D histogram with a 2D probability map constructed by histograms overlap. The five methods belong to three different approaches:
• method 1 and 2 provide a 2D comparison by matching the feature histogram directly over the map and combining the with a product and sum operator, respectively mean of the matched probabilities.
• method 3 synthesizes the map in two 1D histograms, considering for every histogram’s entry the mean and variance values. Then a Mahalanobis distance is computed between these histograms and the π and θ histograms.
• Finally, the 4 and 5 approaches consider a comparison between the feature and the mean
histogram, using respectively intersection and difference between histograms.

The comparison results between classifying rules are reported in Table 1. This shows that the 
first devised classifier outperforms other methods. We believe that in this framework, as 
frequently happens, a multi-classifier could improve performance considerably. With this a 
comparable execution time has been achieved with it. Below a brief explanation of the method is 
exposed.
