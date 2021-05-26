# Task 1: Read and learn (MATLAB, NumPy)

**Tutor**: M.Sc. Vladimir Fadeev

**Form of reports**: PDF file.

The modeling of communication systems is important part of your study and research work. This training task consider OFDM modulation scheme as an example.

![](https://www.qorvo.com/-/media/images/qorvopublic/blog/2017/80211ax/ofdm-vs-ofdma-trucks_4.jpg?la=en&hash=BF9CF410721AB0FF30BA400B34AEFAEC4F8ABC76)

*Fig. 1. OFDM and OFDMA concepts illustration. Source: https://www.qorvo.com/design-hub/blog/80211ax-5-things-to-know* 

The transmitter part of the OFDM based system can be modeled according to the figure 2.

<img src="https://habrastorage.org/getpro/habr/post_images/129/5f3/278/1295f327898f65e98bb1499772dc0b87.png" width="800" />

*Fig. 2. Block scheme of the OFDM based transmitter.*

Your task today is to learn how to model **OFDM frame generator**.
 
1. Read the following MATLAB code:

```octave
clear all; close all; clc

M = 4; % e.g. QPSK 
N_inf = 16; % number of subcarriers (information symbols, actually) in the frame
fr_len = 32; % the length of our OFDM frame
N_pil = fr_len - N_inf - 5; % number of pilots in the frame
pilots = [1; j; -1; -j]; % pilots (BPSK, in fact)

nulls_idx = [1, 2, fr_len/2, fr_len-1, fr_len]; % indexes of nulls

idx_1_start = 4;
idx_1_end = fr_len/2 - 2;

idx_2_start = fr_len/2 + 2;
idx_2_end =  fr_len - 3;


inf_idx_1 = (floor(linspace(idx_1_start, idx_1_end, N_inf/2))).'; 
inf_idx_2 = (floor(linspace(idx_2_start, idx_2_end, N_inf/2))).';

inf_ind = [inf_idx_1; inf_idx_2]; % simple concatenation

inf_and_nulls_idx = union(inf_ind, nulls_idx); %concatenation and ascending sorting
pilot_idx = setdiff(1:fr_len, inf_and_nulls_idx); %numbers in range from 1 to frame length 
% that don't overlape with inf_and_nulls_idx vector

%% Pilots vector
% it should be very convinient to insert pilots if we prepare before "long-vector"
pilots_len_psudo = floor(N_pil/length(pilots)); % floor rounds value to lower integer
% - now we know how many full pilots vectors OFDM-frame consists

mat_1 = pilots*ones(1, pilots_len_psudo); % rank-one matrix - linear algebra trick
resh = reshape(mat_1, pilots_len_psudo*length(pilots),1); % vectorization - linear algebra trick

tail_len = fr_len  - N_inf - length(nulls_idx) ...
				- length(pilots)*pilots_len_psudo; 
tail = pilots(1:tail_len); % "tail" of pilots vector
vec_pilots = [resh; tail]; % completed pilots vector that frame consists

message = randi([0 M-1], N_inf, 1); % decimal information symbols

if M >= 16
	info_symbols = qammod(message, M, pi/4);
else
	info_symbols = pskmod(message, M, pi/4);
end 

%% Frame construction
frame = zeros(fr_len,1);
frame(pilot_idx) = vec_pilots;
frame(inf_ind) = info_symbols
```

2. Answer the following questions:

- What is the purpose of pilots in OFDM frames?
- What does `.'` operator mean in MATLAB?
- How can be constructed matrix of **zeros**, matrix of **ones** and **identity** matrix?
- How can be matrix vectorized in MATLAB?
- How can be random integer values generated in MATLAB?
- How many bits per symbol in 16-QAM? How many in QPSK?
- What kind of modulation scheme has better bit-error ratio performance: QPSK or 16-QAM? BPSK or QPSK? BPSK or 64-QAM? What property determines this?
- What is the basic array-like type of the NumPy module?
- What are the analogs of `linspace`, `union`, `setdiff`, `reshape` in NumPy?


# Task 2: Write and learn (MatLab)

**Tutor**: M.Sc. Vladimir Fadeev

**Form of reports**: PDF file.

Ok, you have already read how a part of the communication system can be modeled during the first practice. It's time to try model whole system by your-self in MatLab! Let us begin from some basics: transmitter, channel, receiver. Use the following block scheme as the reference:

<img src="https://raw.githubusercontent.com/kirlf/CSP/master/MIMO/assets/test-model.png" width="800" />

- **Digital baseband modem**:
 
 **QPSK** with **pi/4** phase rotation: apply [pskmod](https://www.mathworks.com/help/comm/ref/pskmod.html) and [pskdemod](https://www.mathworks.com/help/comm/ref/pskdemod.html) MatLab functions.

> Note: However, you should remember that sometimes written by hand MATLAB code can be even faster than built-in functions and objects (see an [example](https://github.com/kirlf/csp-modeling/blob/master/scripts/fast_qpsk.m))! 

- **Channel**:

Use the **Rayleigh flat fading** with **AWGN** as the channel model. For AWGN use 11 Eb/No points (from 0 to 10 dB).

> See the following tutorial to obtain example of end-to-end communication system modeling (with code!) and required formulas.
>
> [Rician flat fading channel modeling](https://nbviewer.jupyter.org/github/kirlf/CSP/blob/master/MIMO/RicianFlatFadingMATLAB.ipynb)

- **Results vizualization**:

For bit error ratio calculation use [biterr](https://www.mathworks.com/help/comm/ref/biterr.html) or write something like this by your-self.

Plot the resulting bit error ratio curves and verify your results by [berfading](https://www.mathworks.com/help/comm/ref/berfading.html) function.  

- **OFDM case**:

Use the OFDM frame from **Task 1** in the same simulation loop. 

Good luck!


# Hints

1. Read the following slides to obtain more theoretical information: 
https://speakerdeck.com/kirlf/linear-digital-modulations

2. Use the official MATLAB documentation from MathWorks.com (and Google) to obtain information about this programming language.

3. Use the official NumPy documentation from https://numpy.org/doc/stable/ to obtain more information about this library.

4. You can use also Octave (e.g. https://octave-online.net/) to run MATLAB code.

5. It is OK to ask questions.
