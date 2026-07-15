## **Multimodal Gait Dataset (EEG, EMG, IMU, Force Plate)**



This dataset contains multimodal physiological and biomechanical recordings collected from **59** healthy young adults during treadmill walking experiments. Data were recorded at **three** walking speeds (0.5, 0.75, and 1.0 m/s) and include:



1\. Electroencephalography (EEG)

2\. Electromyography (EMG)

3\. Inertial Measurement Unit (IMU) kinematics

4\. Force plate ground reaction forces



The dataset is intended for research in gait analysis, human movement science, neuroengineering, biomechanics, and machine learning applications.





### **Dataset Structure**



###### **dataset\_root/**



├── **EEG\_Data**/  

│   ├── EEG.csv/              EEG recordings in CSV format  

│   ├── EEG.edf/              EEG recordings in EDF format  

│   ├── EEG\_referencing.m     MATLAB script for EEG referencing  

│   └── README.txt            Description of EEG files  



├── **EMG\_Data**/  

│   ├── EMG\_S\[subjectID]\_\[speed].csv   EMG recordings for each subject and walking speed  

│   └── README.txt                     Description of EMG files  



├── **FP\_Data**/  

│   ├── FP\_S\[subjectID]\_\[speed].csv    Force plate recordings for each subject and walking speed  

│   └── README.txt                     Description of force plate data  



├── **IMU\_Data**/  

│   ├── IMU.tar.gz            Compressed archive containing all IMU recordings  

│   └── README.txt            Description of IMU data structure  



├── **subject-info.csv**          Participant demographic and experimental metadata  

├── **README.md**                 Dataset overview and usage instructions  

└── **RELEASE\_NOTES.txt**         Dataset version history





### **EEG Data**



EEG recordings are provided in both **.csv** and **.edf** formats, with 177 files per format corresponding to: 59 participants × 3 walking speeds.



**File naming format:** S\[subjectID]\_\[speed]\_raw

**Example:** S1\_0.5\_raw

**Each file contains:**

* 19 scalp EEG channels
* 2 reference electrodes (A1, A2)
* 3 auxiliary channels (unused)
* Trigger channel
* Acquisition diagnostics

*Signals are provided as raw recordings without software filtering.*





### **EMG Data**



EMG recordings are stored as **CSV** files, with one file per participant per walking speed (169 total recordings).



**File naming format:** EMG\_S\[subjectID]\_\[speed].csv

**Each file includes:**

* Timestamp column
* 12 lower-limb muscle activation signals

**Recorded muscles:**

1. Right tibialis anterior
2. Left tibialis anterior
3. Right vastus lateralis
4. Left vastus lateralis
5. Right rectus femoris
6. Left rectus femoris
7. Right gastrocnemius medial head
8. Left gastrocnemius medial head
9. Right semitendinosus
10. Left semitendinosus
11. Right gastrocnemius lateral head
12. Left gastrocnemius lateral head

*Some recordings are missing due to acquisition issues.*





### <b>IMU Data</b>



IMU kinematic recordings are organized as per-subject, per-speed folders (170 total).



**Folder naming format:** IMU\_S\[subjectID]\_\[speed]



Each folder contains **18 CSV** files representing segment and joint-level measurements such as:

* Segment orientations (quaternions)
* Segment positions
* Linear velocities
* Linear accelerations
* Angular velocities
* Joint angles



Joint angle data follow ISB biomechanical conventions and include measurements for:

* Spine
* Head
* Upper limbs
* Lower limbs
* Major joints (shoulder, elbow, wrist, hip, knee, ankle, ball of foot)





### **Force Plate Data**



Force plate recordings are provided as **CSV** files (173 files total).



**File naming format:** FP\_S\[subjectID]\_\[speed].csv



Each file contains bilateral ground reaction force and center of pressure data:

* Forces: Fx, Fy, Fz
* Moments: Mx, My, Mz
* Center of Pressure: COPx, COPy



*Columns 1–6 correspond to the left leg, while columns 7–12 correspond to the right leg.*





### <b>Subject Information</b>



**subject-info.csv** contains participant metadata including:



* Subject ID
* Demographic information
* Experimental metadata



*This file can be used to link subject characteristics with sensor recordings.*





### **Getting Started**



To begin using the dataset:



1. Download the dataset and extract all compressed folders if needed.
2. Use subject-info.csv to identify participant metadata.
3. Select the modality of interest (EEG, EMG, IMU, or FP).
4. Load the corresponding files using standard analysis tools such as:

* Python (NumPy, Pandas, MNE, SciPy)
* MATLAB
* R
* Julia



*All files are stored in CSV or EDF formats, making them compatible with most scientific software environments.*





