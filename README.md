
# Train your own OpenCV Haar classifier

So in order to train your object you need some positive samples as well as negative samples. You can find more information here (http://docs.opencv.org/doc/user_guide/ug_traincascade.html) about  how to choose positive and negative images. Then put your positive images in positive_images  folder and negative images in negative_images folder. 

1) To get positive images list 

        find ./positive_images -iname "*.jpg" > positives.txt

2) To get negative images list

        find ./negative_images -iname "*.jpg" > negatives.txt

3) Now you need to create some additional positive and negative samples. You can do this by executing below command. This will generate 1500 positive images for you.

        perl bin/createsamples.pl positives.txt negatives.txt samples 1500\
        "opencv_createsamples -bgcolor 0 -bgthresh 0 -maxxangle 1.1\
        -maxyangle 1.1 maxzangle 0.5 -maxidev 40 -w 80 -h 40"

Next step is needs to marge *.vec files which are in samples directory. To do that we use  Naotoshi Seo’s(http://note.sonots.com/SciSoftware/haartraining.html#w0a08ab4) mergevec.cpp tool, which is included in the src directory of the repository.

4) Copy mergevec.cpp file into source directory of OpenCV. 

        cp src/mergevec.cpp ~/opencv-2.4.9/apps/haartraining
        cd ~/opencv-2.4.9/apps/haartraining

5) For linux uses just run this in command line 
        
        g++ `pkg-config --libs --cflags opencv` -I. -o mergevec mergevec.cpp\
        cvboost.cpp cvcommon.cpp cvsamples.cpp cvhaarclassifier.cpp\
        cvhaartraining.cpp\
        -lopencv_core -lopencv_calib3d -lopencv_imgproc -lopencv_highgui -lopencv_objdetect

   Windows uses have to do some more steps to get this working. 
   I’ll show you how to do this on CodeBlocks
   
   1) Create OpenCV project on  CodeBlocks
   
  File->New->Project 

and select OpenCV project and follow steps and you need to provide OpenCV version, bin path, lib path, include      path.Then it will be created empty OpenCV project for you.

   2)Then you need to link some libraries into your project.
   
    Project-> Build Options
In new windows select linker settings and click add button under link libraries. You need to add                      opencv_core,opencv_highgui and opencv_imgproc libraries which are in lib folder(OpenCV build directory) 

  Hope you will able to build project now. 
  
   3)Now copy mergevec in to project directory and run this command to marge *.vec files 
        
        find ./samples -name '*.vec' > samples.txt 
        ./mergevec samples.txt samples.vec

Now it is time train your classifier. You have two options copy opencv_haartraining and opencv_traincascade into your project directory from opencv source directory.I will show you how to do with  opencv_haartraining tool.

        ./opencv_haartraining -data classifier -vec samples.vec -bg negatives.txt -nstages 10 -nsplits 1 -minhitRate 0.999 -maxfalsealarm 0.5 -npos 500 -nneg 500 -w 80 -h 40 -mem 200 -bt RAB
