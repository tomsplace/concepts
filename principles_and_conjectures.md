# What if ...

## Everything is a variable

There are no constants, only slowly changing variables, +/- allowable error

i.e., Heraclitus was right

* sensor location ... can shift during an experiment
* sensor material ... can change during an experiment, tin disk -> quick insert
* "age at time of testing" changes over the course of testing at 1 sec/sec
* name ... can be changed by law
* gender ... can be changed by surgery
* experimenter ... see name, gender

This has consequences for what goes in an EEG data header.

## For digital EEG data **everything** is an epoch.

> Everything with a beginning and end is an epoch.  
> All dEEG data have a beginning and end.  
> :. All dEEG data are epochs.  

When analog bioamp outputs are digitally encoded and streamed to 
digital memory (spinny disk, SSD, RAM buffer, etc.), there is always a
first and last sample.

This creates an epoch of dEEG which might be ...

  * very short, e.g., a single sample.
  * somewhat longer, a few hundred or thousand samples e.g., spanning an experimental trial.
  * still longer, a few hundred thousand samples, e.g., spanning a block of multiple experimental trials.
  * still longer, some millions of samples, e.g., spanning a night of sleep

Individual epochs can be collected into a set, possibly but not necessarily, ordered as 
a sequence along some attribute, e.g., time.

A set of epochs may be regular, i.e., all of the same length, or not.

In EEG research "epoch" generally refers to regular length epochs of single trial 
dEEG which is a special case of epoch in the general sense. "Continous" dEEG
tends to refer to longer block- or experiment-lenth epochs from which single trial 
"epochs" are selected.

Drawing a distinction between "continous" and "epoched" dEEG in talking about the
data is harmless.

But it may not be the best way to organize dEEG data analysis software data 
structures, c.f. EEGLAB EEG.data 2-D "continous" vs. 3-D "epochs"; MNE Python 
mne.Raw vs. mne.Epochs. 

Related observations:

> All dEEG persistent storage stores a set (possibly ordered) of epochs epochs.  

> All dEEG data analysis is over a set (possibly ordered) of epochs.

> "An" epoch is a set with cardinality 1, i.e., a set of epochs

> "An" epoch of missing data, e.g., dropouts, artifacts, has first and last sample == null/NA/None, 
> i.e., missing data is still set of epochs.

Conjecture: For dEEG data analysis software, the basic data structures
should be sets (possibly ordered) of epochs.

Because that is what there really is.

On this approach "continous" and "epoched" data differ in the number and length of
epochs in the set, but not data type.

Some familiar scenarios:

* A single subject, single session EEG data file with recording pauses is a temporally 
  ordered set (sequence) of longish, irregular epochs.
* Multiple data files for a single subject, single session is a set of longish, irregular epochs
* Multiple data files for a single subject, multiple sessions is is a set of longish, irregular epochs
* Conventional single-trial "epochs" are a smallish set of shortish, regular epochs
* Single trials across all subjects in an experiment is a largish set of shortish, regular epochs.

Iterating transformations/functions over individual epochs will be commonplace.

Concatenation of the naturally occurring epochs into a new artificial epoch of 
longer-but-not-actually continuous epochs will be the rare exception since
since concatenation obliterates the natural and true boundaries of the data. 

Conjectures:

Making sets of epochs the primitive or base class may have beneficial consequences
for data analysis.
  
A library of data transformations can be or at least begin with functions from sets epochs
to sets of epochs. The dimensionalty of the sets and the data types may change but 
input and output classes do not. E.g., re-referencing (1-1), frequency filtering (1-1), 
averaging (many-1), time-frequency analysis (1-many).

This systematicity could simplify pipelining data transformations, e.g., if there is no functional
difference between digitally filtering a single "raw continuous" vs. a set "single-trial epochs".
The difference is the number and length of the epochs, not the data structure. 

And perhpas it would reduces some of the self-infllicted difficulties tracking where
the data is and isn't (event tracks, [EM]EG, data tags/experimental variables) across 
upsampling, downsampling and data subsetting.

## dEEG is mostly missing data, NAs

The universe, we are told, is mostly empty space populated occasionally with widely spaced nuggets of matter. Matter is the exception, emptiness is the rule. The appearance of solid objects is an illusion, but it doesn't matter if we can manipulate them for practical purposees.

Digitally encoded instrument outputs are mostly missing data, populated with widely spaced nuggets of measurement. Measured values are the exception, missing data the rule. The appearance of continuous measurement is an illusion, but it doesn't matter in practice if we can manipulate them for practical purposes.

Conjecture: since missing data is the rule not the exception, it seems prudent to build a systematic treatment into digital representations from the beginning rather than patching in it in ad hoc after everything else is in place. 

There are floats, ints, uints, and bools but IEEE, MATLAB, R, python, pandas, statsmodels, handle things somewhat the same and somewhat differently or not at all.

Research on multiscale data representation grapples with these issues.
