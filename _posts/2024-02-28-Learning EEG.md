# EEG Tutorial
1. Where do we start:
   [EEGLAB Tutorial](https://eeglab.org/tutorials/)
   
2. A clean/resting state human EEG dataset to test your rejection:
   R:\Ferris-Lab\liu.chang1\DATA\SampleData\2019-12-19_humanExampleData\Raw
   
3. Downey et al. 2023: iCanClean to remove motion artifacts with the dual-layer electrode design
   [Paper on iCanClean](https://www.mdpi.com/1424-8220/23/19/8214)
   
## Some useful code blocks    
High-pass filter at 1Hz
``` matlab
EEG = pop_eegfiltnew(EEG, 'locutoff',1,'chantype',{'EEG' 'EMG' 'Noise'});
```

Keep only a subset of chans
``` matlab
EEG_chans = find(strcmpi('EEG',{EEG.chanlocs.type})); %define channels
EMG_chans = find(strcmpi('EMG',{EEG.chanlocs.type}));
Noise_chans = find(strcmpi('Noise',{EEG.chanlocs.type}));

EEG = pop_select( EEG, 'channel', sort([EEG_chans, EMG_chans,Noise_chans])); %select subset of channels
EEG = pop_select( EEG, 'channel', sort([EEG_chans])); %select just EEG channels

%update channel labels again just in case
EEG_chans = find(strcmpi('EEG',{EEG.chanlocs.type})); 
EMG_chans = find(strcmpi('EMG',{EEG.chanlocs.type}));
Noise_chans = find(strcmpi('Noise',{EEG.chanlocs.type}));
```

Re-reference and do cleanline
``` matlab
EEG = rerefC2CN2NExt2Ext_func(EEG,fullRankAvRefBool);
EEG = pop_cleanline(EEG, 'bandwidth',2,'chanlist',[EEG_chans EMG_chans Noise_chans],'computepower',1,...
    'linefreqs',[60 120],'newversion',0,'normSpectrum',0,'p',0.01,'pad',2,'plotfigures',...
0,'scanforlines',1,'sigtype','Channels','taperbandwidth',2,'tau',100,'verb',1,'winsize',4,'winstep',1);
```

## Useful preprocessing code block
Step 1: Remove bad channels based on standard deviation and kurtosis
[auto_rejection_code](https://github.com/changliu-99/Reference-code/blob/37ebdabc514e624424b7473eb1a317b228b44f14/EEG/Preprocessing/autoRejCh_func_CL.m)
``` matlab
EEG = autoRejCh_func_CL(EEG,threshold);
```
