# Conversational transaction bot
The Conversational Transaction Bot executes transactions on user commands. A user initiates a dialogue with a text query to a bot. The bot understands the user text, initiates execution of operations at the backend, and responds to the user in text. The dialogue continues until, normally, the user terminates the dialogue when its requests have been serviced by the bot. The implementation is based on Deep Learning Transformers.   
## Requirements
* PyTorch version >= 1.7.1
* Python version >= 3.8.5
* PyTorch-Lightning version >= 1.2.1
* Huggingface Transformers version >= 4.3.3
* Tensorboard version >= 2.4.1
## Installation
```
pip3 install transformers
pip3 install pytorch-lightning
pip3 install tensorboard
git clone https://github.com/vineetk1/conversational-transaction-bot.git
cd conversational-transaction-bot
```
Note that the default directory is *conversational-transaction-bot*. Unless otherwise stated, all commands from the Command-Line-Interface must be delivered from the default directory.
## Download DSTC2 dataset
1. Go to https://fb-public.app.box.com/s/chnq60iivzv5uckpvj2n2vijlyepze6w 
1. Download *dialog-bAbI-tasks_1_.tgz* in directory *data*  

Verify that the current working directory is *data*.    
```
tar zxvf dialog-bAbI-tasks_1_.tgz
rm dialog-bAbI-tasks_1_.tgz
```
Verify that the DSTC2 dataset is in the directory *data/dialog-bAbI-tasks*.   
## Convert DSTC2 dataset to the default format
Convert dataset into a default format. An example of the default format is shown in the file *convert_to_default_formats/default_format_example.md*.   

Verify that the current working directory is the default directory. Following command converts the downloaded dataset to the default format, and saves it in the files - *defaultFormat.train, defaultFormat.valid, defaultFormat.test* - of the directory *data/dialog-bAbI-tasks/dstc2*:
```
python3 convert_to_default_formats/dstc2_to_defaultFormat.py
```
Note that the above program converts the DSTC2 dataset to the default format. A new conversion program will have to be implemented for a dataset that has a different format from that of the DSTC2 dataset. 
## Train, validate, and test a model
Following command trains a model, saves the last checkpoint plus checkpoints that have the lowest validation loss, runs the test dataset on the checkpointed model with the lowest validation loss, and outputs a Perplexity value of the model:
```
python3 ctbMain.py input_param_files/distilgpt2_dstc2
```
The user-settable hyper-parameters are in the file *input_param_files/distilgpt2_dstc2*. An explanation on the contents of this file is at *input_param_files/README.md*. A list of all the hyper-parameters is in the <a href="https://www.pytorchlightning.ai" target="_blank">PyTorch-Lightning documentation</a>, and any hyper-parameter can be used.    
To assist in Training, the two parameters *auto_lr_find* and *auto_scale_batch_size* in the file *input_param_files/distilgpt2_dstc2* enable the software to automatically find an initial Learning-Rate and a Batch-Size respectively.    
As training progresses, graphs of *"training-loss vs. epoch #"*, *"validation-loss vs. epoch #"*, and "learning-rate vs. batch #" are plotted in real-time on TensorBoard. Training is stopped by typing, at the Command-Line-Interface, the keystroke ctrl-c. The current training information is checkpointed, and training stops. Training can be resumed, at some future time, from the checkpointed file.   
Testing calculates the Perplexity of the model from the test dataset. A detailed statistics on the model is generated in the files *failed_dialogs_stat.txt* and *passed_dialogs_stat.txt*. The *failed_dialogs_stat.txt* file has information about the dialogs that failed, and *passed_dialogs_stat.txt* has information about the dialogs that passed.
## Resume training, validation, and testing a model with same hyper-parameters
Resume training a checkpoint model with the same model- and training-states by using the following command:
```
python3 ctbMain.py input_param_files/distilgpt2_dstc2-resume_training
```
The user-settable hyper-parameters are in the file *input_param_files/distilgpt2_params-resume_training*.  An explanation on the contents of this file is at *input_param_files/README.md*.
## Start training, validation, and testing a model with different hyper-parameters
Start training a checkpoint model with the same model-state but different hyperparameters for the training-state by using the following command:
```
python3 ctbMain.py input_param_files/distilgpt2_dstc2-load_change_params
```
The user-settable hyper-parameters are in the file *input_param_files/distilgpt2_dstc2-load_change_params*.  An explanation on the contents of this file is at *input_param_files/README.md*.
## Interact with the deployed model
Work In Progress.
## Fine-tuning Distilgpt2 with DSTC2 dataset
### &emsp; &emsp; Using NAG with varying initial Learning Rates
**Hyperparameters:**    
``*`` Optimizer Parameters -- SGD, lr: variable, momentum: 0.9, weight_decay: 0, dampening: 0, nesterov: True   
``*`` LR-Scheduler Parameters -- None   
<img src=images/tensorboard,val_loss-5_epochs,nag.png width=800 height=500>     
*Graph: Validation-loss vs. Epoch for varying initial Learning-Rates.   
(Color of curve, Learning-Rate, Val-loss at epoch 0 and epoch 5) where en = euler number = 2.71828 -- (Aqua, en<sup>-12</sup>, 1.69, 0.8783), (Brown, en<sup>-11</sup>, 1.15, 0.6272), (Blue, en<sup>-10</sup>, 0.814, 0.4331), (Pink, en<sup>-6</sup>, 0.6802, 0.1893), (Orange, en<sup>-9</sup>, 0.6443, 0.2957), (Green, en<sup>-5</sup>, 0.6143, 0.1748), (Grey, en<sup>-8</sup>, 0.5248, 0.2281), (Green, en<sup>-7</sup>, 0.4954, 0.1887)*   
### &emsp; &emsp; Using Adam with varying initial Learning Rates
**Hyperparameters:**    
``*`` Optimizer Parameters -- Adam, lr: variable, betas: (0.9, 0.999), eps: 1e-8, weight_decay: 0, amsgrad: False   
``*`` LR-Scheduler Parameters -- None   
<img src=images/tensorboard,val_loss-5_epochs,adam.png width=800 height=500>     
*Graph: Validation-loss vs. Epoch for varying initial Learning-Rates.   
(Color of curve, Learning-Rate, Val-loss at epoch 0 and epoch 5) where en = euler number = 2.71828 -- (Grey, en<sup>-13</sup>, 0.2225, 0.206), (Green, en<sup>-12</sup>, 0.3575, 0.1593), (Pink, en<sup>-11</sup>, 0.23, 0.1562), (Aqua, en<sup>-10</sup>, 0.1859, 0.1726), (Orange, en<sup>-7</sup>, 0.1814, 0.1971), (Blue, en<sup>-8</sup>, 0.1647, 0.2095), (Brown, en<sup>-9</sup>, 0.1634, 0.1924)*   
### &emsp; &emsp; Results
**Hyperparameters:**    
``*`` Optimizer Parameters -- Adam, lr: 1e-05, betas: (0.9, 0.999), eps: 1e-8, weight_decay: 0, amsgrad: False   
``*`` LR-Scheduler Parameters -- ReduceLROnPlateau, mode: min, patience: 6, factor: 0.1    
<img src=images/train-train_loss_0.1683,val_loss_0.1581.png width=400 height=300> <img src=images/val-train_loss_0.1683,val_loss_0.1581.png width=400 height=300>   
&emsp; &emsp; &emsp;*Graph: Training-loss vs. Epoch.* &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp; &emsp;  *Graph: Validation-loss vs. Epoch.*    
Epoch 3 has the lowest validation loss of 0.1581 that is lower than the training loss of 0.1683. Following is the dialog statistics on the test set:     
Statistics on the test set    
--------------------------    
\*\* Number of turns = 11237    
   \*\* Percent of turns with truncated inputs = (426/11237 x 100) = 3.79%    
   \*\* Percent of turns that passed = (5142/11237 x 100) = 45.76%    
      ** Percent of turns that passed with truncated inputs = (195/426 x 100) =
          45.77%
      ** Percent of turns that passed with untruncated inputs = (4947/10811 x
          100) = 45.76%
   ** Percent of turns that passed at each turn-number in dialogs -- (Turn # in
       dialogs: # of such turns that passed/total number of such turns x 100 =
       result) -- (1: 1117/1117 = 100.00%), (2: 43/1117 = 3.85%), (3: 245/1117 =
       21.93%), (4: 362/1117 = 32.41%), (5: 417/1116 = 37.37%), (6: 503/1098 =
       45.81%), (7: 520/1022 = 50.88%), (8: 491/864 = 56.83%), (9: 383/686 =
       55.83%), (10: 302/523 = 57.74%), (11: 224/393 = 57.00%), (12: 173/287 =
       60.28%), (13: 93/199 = 46.73%), (14: 76/149 = 51.01%), (15: 48/107 =
       44.86%), (16: 29/77 = 37.66%), (17: 29/60 = 48.33%), (18: 24/50 =
       48.00%), (19: 19/39 = 48.72%), (20: 13/28 = 46.43%), (21: 9/20 = 45.00%),
       (22: 6/14 = 42.86%), (23: 4/11 = 36.36%), (24: 2/8 = 25.00%), (25: 2/6 =
       33.33%), (26: 4/5 = 80.00%), (27: 2/4 = 50.00%), (28: 1/2 = 50.00%), (29:
       1/1 = 100.00%)
** Number of dialogs = 1117
   ** Percent of dialogs that passed= 1/1117 x 100 = 0.09%
      ** (# of turns in dialog: # of such dialogs that passed/total number of
          such dialogs x 100 = result) -- (4: 0/1 = 0.00%), (5: 0/18 = 0.00%),
          (6: 0/76 = 0.00%), (7: 1/158 = 0.63%), (8: 0/178 = 0.00%), (9: 0/163 =
          0.00%), (10: 0/130 = 0.00%), (11: 0/106 = 0.00%), (12: 0/88 = 0.00%),
          (13: 0/50 = 0.00%), (14: 0/42 = 0.00%), (15: 0/30 = 0.00%), (16: 0/17
          = 0.00%), (17: 0/10 = 0.00%), (18: 0/11 = 0.00%), (19: 0/11 = 0.00%),
          (20: 0/8 = 0.00%), (21: 0/6 = 0.00%), (22: 0/3 = 0.00%), (23: 0/3 =
          0.00%), (24: 0/2 = 0.00%), (25: 0/1 = 0.00%), (26: 0/1 = 0.00%), (27:
          0/2 = 0.00%), (28: 0/1 = 0.00%), (29: 0/1 = 0.00%)
      ** (# of consecutive turns that passed, counting from beginning of dialog:
          # of occurrences of such consecutive turns) -- (1: 1074), (2: 38), (3:
          3), (4: 1), (6: 1)
--------------------------------------------------------------------------------
TEST RESULTS
{'test_loss_step': 0.29131555557250977,
 'test_loss_step_epoch': 0.3567347228527069,
 'test_perplexity': 1.4286484718322754}
--------------------------------------------------------------------------------
