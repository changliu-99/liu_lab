# EEG Tutorial
1. Where do we start:
   [EEGLAB Tutorial]https://eeglab.org/tutorials/
   
2. A clean/resting state human EEG dataset to test:
   R:\Ferris-Lab\liu.chang1\DATA\SampleData\2019-12-19_humanExampleData\Raw
   
3. iCanClean to remove motion artifacts with the dual-layer electrode design:
   [Paper on iCanClean]https://www.mdpi.com/1424-8220/23/19/8214
   
## Some useful code blocks    
High-pass filter at 1Hz
```
EEG = pop_eegfiltnew(EEG, 'locutoff',1,'chantype',{'EEG' 'EMG' 'Noise'});
```
Keep only a subset of chans
```
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
```
EEG = rerefC2CN2NExt2Ext_func(EEG,fullRankAvRefBool);
EEG = pop_cleanline(EEG, 'bandwidth',2,'chanlist',[EEG_chans EMG_chans Noise_chans],'computepower',1,...
    'linefreqs',[60 120],'newversion',0,'normSpectrum',0,'p',0.01,'pad',2,'plotfigures',...
    0,'scanforlines',1,'sigtype','Channels','taperbandwidth',2,'tau',100,'verb',1,'winsize',4,'winstep',1);
```
