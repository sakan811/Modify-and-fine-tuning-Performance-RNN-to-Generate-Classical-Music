# Modify and Fine-tune Performance RNN to Generate Classical Music

This is my research project for one of the modules I'm taking for my Master's degree.     
I might continue with this further, even though my research project finishes.

My goal is to fine-tune and modify the Performance RNN, a model from the Magenta project, to enhance its performance to generate better classical music.

I will compare the original fine-tuned pre-trained Performance RNN model to the modified and fine-tuned version of the model.    

## Training
All of the models used are pre-trained models.     

The models were fed with 1329 MIDI and MusicXML files in total.     
40 MusicXML files were from Musescore and they are all public domain.   
The rest was from Aligned Scores and Performances (ASAP) dataset: https://github.com/fosfrancesco/asap-dataset    
     
The dataset will be divided into 2 datasets, training, and evaluation, with a ratio of 90:10.    

## Table of Contents
- [Result (Original Model)](#result-original-model)
- [Result (Modified Model)](#result-modified-model)
- [Further Use](#further-use)

## Result (Original Model)
This is the original pre-trained Performance RNN model that was fine-tuned with the classical music datasets.    

**Batch size of 48** was used, with **drop rate** of **50 percent**.        
The rest of the hyperparameters' setting was the default.  

**Changelog:**
- At around 65,000 steps, the learning rate was changed from 0.001 to 0.002 to make the model learns faster.
- At around 71,000 steps, the learning rate was changed back to 0.001 as the model's performance worsened.
  
**Generated Music Samples:**      
Sample 1: https://drive.google.com/file/d/1JSktY40pAn1J_v1ifLsBPnGg5zvm3xfl/view?usp=drive_link   
Sample 2: https://drive.google.com/file/d/1VMXVVdngXHFpF1sY8EPsqQdwbTCyrF1v/view?usp=drive_link   
Sample 3: https://drive.google.com/file/d/1nLKE7EGWrH9mE1OX0Y6JEhm944AYsKFc/view?usp=drive_link   

## Result (Modified Model)
**The model was modified by**:     
-Adding the custom loss function that adheres more to the rhythm and harmonic structure of classical music.       
-Adding the early stop function to make the model stops the training when the loss doesn't improve for a certain number of steps or when the loss value reaches the target loss.     

**Batch size of 48** was used, with **drop rate** of **50 percent**.               
The rest of the hyperparameters' setting was default.     
    
## List of Modified files
- **events_rnn_train.py**
    - **Implemented early stopping**
        - You can set the "mode", "patience", and the "target loss" or "target accuracy" according to the mode
        - There are 2 modes: "min" and "max"
            - "min" mode is for monitoring loss value and target loss has to be set. 
                - training will stop when the target loss is reached or loss isn't improved for a certain number of steps
                - If you want to use "min" mode, the following command line is an example:
                ```
                --early_stop='mode=min,target_loss=0.12,patience=10' \
                ```
            - "max" mode is for monitoring accuracy and target accuracy has to be set. 
                - training will stop when the target accuracy is reached or accuracy isn't improved for a certain number of steps
                - If you want to use "max" mode, the following command line is an example:
                ```   
                --early_stop='mode=max,target_acc=0.9,patience=10' \
                ```
            - the number of steps that the early stopping algorithm will tolerate is defined by the "patience" variable multipied by the "summary_frequency" variable
            - the "summary_frequency" is 10. 
            - the default "patience" is 100. 
                - So if you want the algorithm to tolerate for 1000 steps when the loss value isn't improved before stopping the training, you can set the "patience" to 100.
- **events_rnn_graph.py**
    - **Modified the loss function**
        - Added **Rhythm loss** and **Harmonic progression loss** on top of the original loss function.    
            - To enable Rhythm loss, use this following command line:     
                ```
                --rhythm_loss \
                ``` 
            - To enable Harmonic progression loss, use this following command line:     
                ```
                --harmony_loss \
                ``` 
    - **Added L1 Regularization**
        - Added **L1 Regularization** on top of the original loss function.  
            - To enable L1 Regularization, use this following command line:     
                ```
                --l1_regular
                ```   
- **performance_rnn_train.py**
    - Added command line flags for Early Stopping, Rhythm Loss, Harmonic Progression Loss, and L1 Regularization, so they can be controlled manually.
    - Added the **parse_early_stopping_params** function to parse a string containing Early Stopping parameters.
    - Modified the **main** function so that the flags and the added function can work properly.

## Further Use
You can look at this page for the tutorial on how to use the Performance RNN   
https://github.com/magenta/magenta/tree/main/magenta/models/performance_rnn
