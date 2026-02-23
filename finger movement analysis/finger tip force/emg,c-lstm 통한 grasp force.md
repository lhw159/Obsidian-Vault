## Learning and Online Replication of Grasp Forces from Electromyography Signals for Prosthetic Finger Control 

![](file:///C:/Users/LEEHYE~1/AppData/Local/Temp/msohtmlclip1/01/clip_image002.png)
Robin Arbaud∗1,2, Elisa Motta∗1, Marco Domenico Avaro3, Stefano Picinich3, Marta Lorenzini1, and Arash Ajoudani1

### i.        Introduction

Why is this research needed?

1.     Lack of research about partial hand prothesis for finger amputation patient

Most of prosthetic hand research concentrate on transcarpal amputation (entire hand loss), not really for finger loss. But 90% of upper limb amputation cases involve partial hand loss. So, advance of prosthetic fingers not only hand will increase the life quality of patients.

2.     Promote robot controllability through human intention

Most of entire hand follow pre-programmed grasping pattern through using turn-off switch or button. But it is hard to catch human intention directly and uncomfortable.

Since of reasons above, this paper proposes force control prosthetic finger system based on EMG(Electromyography) signal. They focus on relationship of ‘EMG signal’ and ’fingertip force’.

### ii.         Fingertip force estimation

To model the relationship between ‘EMG signal’ and ’fingertip force’ three type of regression algorithms were considered: Random Forest, Gradient Boosting, C-LSTM (convolutional long short term memory, two layer with hidden size 50 and 30).

### iii.        Prothesis system

They used body powered partial finger prosthetic developed by AirWorks. It is a 2-joint underactuated finger, with a single tendon controlling. First measure emg signal from arm-> estimate force through AI model in ‘fingertip force estimation’ section->estimate tension of actuator (tension command)->control actuator through equation ![](file:///C:/Users/LEEHYE~1/AppData/Local/Temp/msohtmlclip1/01/clip_image004.png).

Tension estimation->emg data+actuator position (angle of joint) => bidimensional model for calculate tension->plot tension/finger-tip-force for various actuator position averaging above curves to make it in unidimensional model, since the angle of joint and finger-tip-force is not really correlated for real hand.

### iv.         Experiments

EMG Signal processing: From flexor digitorum superficialis, extensor digitorum superficialis. 20-200Hz bandpass 4th order Butterworth filter, 50Hz notch filter. RMS with 500ms and 200ms

Experimental setup and protocol:

1.     Force estimation

10 normal subjects (5 male, 5 female), measure Maximum Voluntary Contraction (MVC) of index finger during 30s isometric flexion. Subjects were asked to press with their index finder on F/T sensor fixed on table

2.     Prosthetic control

4 normal subjects from experiment 1. Use sub index finger (in paper supernumerary finger) of prosthetic next to index finger. press F/T sensor using prosthetic finger.

Experimental analysis:

Force estimation-> Coefficient of determination (R-squared), Root Mean Squared Error (RMSE)

Prosthetic control-> Analysis in three criteria. Force following (command and output), force targeting (pattern and command), force reaching (pattern and output)

### v.        Result and Discussion

Force estimation

C-LSTM show even better performance than other models in both of training, test sets with the highest R-squared value and lowest RMSE

Prosthetic control

In three criteria, results show satisfying performance in range of <1.6 for RMSE. Also, it showed that we can use wrist rotating or other movement to make proper contraction of muscle to improve performance of prosthetic, and it led to low RMSE in force reaching

### vi.         Conclusion

Through force estimate model and tension exerting system of prosthetic without direct force control capability, successfully show satisfying performance in term of fingertip force. And, EMG-based force control showed a tolerate for little inaccuracy.