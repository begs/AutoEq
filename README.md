# AutoEQ
AutoEQ is a project for equalizing headphone frequency responses automatically and it achieves this by parsing
frequency response measurements and producing a equalization settings which correct the headphone to a neutral sound.
This project currently has 700+ onear headphones covered in the
[results](https://github.com/jaakkopasanen/AutoEq/tree/master/results) folder. Results are organized by
type/compensation/source/headphone so a Sennheiser HD 650 measured by Innerfidelity and tuned to
[Innerfidelity 2017 compensation curve](https://www.innerfidelity.com/content/compensation-curve-innerfidelity-measurements-dialog-part-1)
would be found in
[onear/innerfidelity2017/innerfidelity/Sennheiser HD 650](https://github.com/jaakkopasanen/AutoEq/tree/master/results/onear/innerfidelity2017/innerfidelity/Sennheiser%20HD%20650).
See [Usage](https://github.com/jaakkopasanen/AutoEq#usage) for instructions how to use the results with
[EqualizerAPO](https://sourceforge.net/projects/equalizerapo/) and
[Results](https://github.com/jaakkopasanen/AutoEq#results) for details about parameters and how the results were
obtained.

AutoEQ is not just a collection of automatically produced headphone equalization settings but also a tool for equalizing
headphones for yourself. `frequency_response.py` provides methods for reading data, equalizing it to a given target
response and saving the results for usage with EqualizerAPO. It's possible to use different compensation (target)
curves, apply tilt for making the headphones brighter/darker and adding a bass boost. It's even possible to make one
headphone sound (roughly) like another headphone. For more info about equalizing see
[Equalizing](https://github.com/jaakkopasanen/AutoEq#equalizing)

Third major contribution of this project is the measurement data, compensation curves and calibration curves all in a
numerical format. Everything is stored as CSV files so they are easy to process with any programming language or even
Microsoft Excel. See [Compensation](), [Calibration]() and [Data Processing]() for more technical description about how
things were obtained and processed.

![](https://raw.githubusercontent.com/jaakkopasanen/AutoEq/master/results/onear/SBAF-Serious-brighter/innerfidelity/HiFiMAN%20HE400S/HiFiMAN%20HE400S.png)
*HiFiMAN HE400S equalization results plotted*


### Usage
Equalization settings produced by AutoEQ are EqualizerAPO GraphicEQ configuration lines, one for each headphone
processed. They look like this:
````
GraphicEQ: 10 -84; 20 6.0; 22 6.0; 23 6.0; 25 6.0; 26 6.0; 28 6.0; 30 6.0; 32 6.0; 35 6.0; 37 6.0; 40 6.0; 42 5.9; 45 5.7; 49 5.4; 52 5.2; 56 4.9; 59 4.7; 64 4.3; 68 4.1; 73 3.8; 78 3.5; 83 3.2; 89 2.8; 95 2.4; 102 2.1; 109 1.7; 117 1.2; 125 0.8; 134 0.5; 143 0.3; 153 0.2; 164 0.1; 175 0.1; 188 0.1; 201 0.1; 215 0.2; 230 0.3; 246 0.4; 263 0.3; 282 0.3; 301 0.2; 323 0.1; 345 0.0; 369 0.0; 395 -0.2; 423 0.3; 452 0.5; 484 0.9; 518 1.2; 554 1.1; 593 1.1; 635 1.0; 679 1.0; 726 0.8; 777 0.6; 832 0.3; 890 0.1; 952 -0.1; 1019 -0.1; 1090 -0.1; 1167 0.7; 1248 0.5; 1336 0.1; 1429 -0.1; 1529 0.1; 1636 0.3; 1751 0.7; 1873 1.3; 2004 1.7; 2145 1.7; 2295 2.4; 2455 2.7; 2627 3.0; 2811 2.4; 3008 2.3; 3219 2.5; 3444 2.6; 3685 2.6; 3943 2.4; 4219 0.7; 4514 -0.1; 4830 2.1; 5168 5.4; 5530 6.0; 5917 4.2; 6331 1.9; 6775 2.1; 7249 1.3; 7756 0.3; 8299 0.0; 8880 0.0; 9502 0.0; 10167 0.0; 10879 0.0; 11640 0.0; 12455 0.0; 13327 0.0; 14260 0.0; 15258 0.0; 16326 0.0; 17469 0.0; 18692 0.0; 20000 0.0
````
HeSuVi is highly recommended to be used with AutoEQ results. HeSuVi provides a convenient graphical user interface for
adjusting the equalizer, toggling eq on and off, adjusting preamp and saving and restoring multiple different
configurations making it very easy to compare different eq settings. To load a eq into HeSuVi, close HeSuVi and replace
contents of HeSuVi's eq file `C:\Program Files\EqualizerAPO\config\config.txt` with the produced GraphicEQ line. Then
start HeSuVi and adjust global volume for both channels to highest eq value. You can save the configuration with profile
manager in bottom right corner.

![](https://raw.githubusercontent.com/jaakkopasanen/AutoEq/master/img/HeSuVi.PNG)

It is also possible to use EqualizerAPO without any graphical user interface by copying the result into the end of the
EqualizerAPO configuration file `C:\Program Files\EqualizerAPO\config\config.txt`. EqualizerAPO will update live as
soon as the file is saved. Adding a hash `#` to the beginning of the line and saving the file will disable EQ making it
possible to compare sound when using and not using EQ. When using EqualizerAPO like this it's **imperative** to apply a
preamp which brings everything down as much as the highest positive gain is, this can be done by adding this line also.
This example assumes that equalization has maximum positive gain of 6dB. Exact amounts are displayed in the results.
````
Copy: L=-6.0dB*L R=-6.0dB*R
````

If you are already using Peace GUI for EqualizerAPO you'll have to disable the configurations made by it because Peace
doesn't work with GraphicEQ and you don't want to have two EQs on at the same time. Producing parametric eq settings
for Peace users could be a consideration for the future if enough people want it.

### Results
The main princible used by AutoEQ for producing the equalization function is to invert error function. Error is the
difference between raw microphone data and the compensation (target) curve. If headphone's frequency response is 4 dB
below the target at 20 Hz equalization function will have +4 dB boost at 20 Hz. In reality simply inverting the error is
not sufficient since measurements and equalization have several problems that need to be addressed, see
[Technical Challenges](https://github.com/jaakkopasanen/AutoEq#technical-challenges) for more details.

Results provided in this project have all the on-ear headphone measurements from
[Innerfidelity](https://www.innerfidelity.com/headphone-measurements) and [Headphone.com](http://graphs.headphone.com/)
with 4 different target responses:
- Innerfidelity 2017
- Headphone.com
- Modified version of Innerfidelity target by @Serious user on Super Best Audio Friends forum
- Sonoma Model One measurement by Innerfidelity

All but the Headphone.com compensation curve are targeted to Innerfidelity measurements. But all targets have results
also for Headphone.com data, in these cases the Headphone.com data was calibrated to Innerfidelity measurement system
before applying the compensation.

Innerfidelity 2017 compensation curve is the result of Tyll Hertsens calibrating his measurement head on the Harman
reference listening room and is a significant improvement over the old compensation curve used in PDFs. However 2017
curve seems to underestimate 2 to 5 kHZ region by several dB leading the equalization to boost those frequencies too
much.

Headphone.com compensation curve is used by Headphone.com with their Frequency Response graphs but this seems to
underestimate treble even more than the 2017 Innerfidelity curve leading to even brighter equalization.

Recommended compensation curve is the modified version of Innerfidelity target curve produced by Serious user on Super
Best Audio Friends forum. This curve doesn't have the glaring treble problems of previously mentioned targets but is
quite well balanced overall. Curve was turned into a compensation for raw microphone data and tilted 0.3 dB / octave
brighter. See the [forum thread](https://www.superbestaudiofriends.org/index.php?threads/innerfidelity-fr-target.5560/)
for discussion about the original target.

Fourth target is the raw measurement data of Sonoma Model One headphone system as measured by Innerfidelity. This is an
experiment in equalizing headphones to sound like other headphones. Sonoma Model One is reasonably neutrally tuned
headphone and as such work as a quite good equalization target. I personally prefer the modified SBAF-Serious target
though.

None of these targets have bass boost seen in Harman target responses and therefore a +4dB boost was applied for all
results. Above 6 to 8kHz data is filtered more heavily to avoid measurement artifacts and no positive gain (boost) is
applied. In the upper treble measurements are less reliable and boosting them too much will cause serious problem while
having some narrow dips is not a problem at all.

### Equalizing
`frequency_response.py` is the tool used to produce the equalization results from data measurement data. There is no
fancy graphical user interface but instead it is used from command line.

#### Installing
- Download [AutoEQ zip](https://github.com/jaakkopasanen/AutoEq/archive/master.zip) and exctract to a convenient location.
- Download and install [Python3](https://www.python.org/getit/). Make sure to check *Install Python3 to PATH*
- Install virtualenv. Run this on command prompt. Search `cmd` in Windows start menu.  
````commandline
pip install virtualenv
````
- Go to AutoEQ location  
````commandline
cd C:\path\to\AutoEq-master
````
- Create virtual environment  
````commandline
virtualenv venv
````
- Activate virtualenv  
````commandline
.\vevn\scripts\activate
````
- Install required packages  
````commandline
pip install -r requirements.txt
````
- Verify installation  
````commandline
python frequency_response.py -H
````

When coming back at a later time you'll only need to activate virtual environment again
````commandline
cd C:\path\to\AutoEq-master
.\vevn\scripts\activate
````

#### Command Line Arguments
````
usage: frequency_response.py [-h] --input_dir INPUT_DIR --output_dir
                             OUTPUT_DIR [--calibration CALIBRATION]
                             [--compensation COMPENSATION] [--equalize]
                             [--bass_boost BASS_BOOST] [--tilt TILT]
                             [--max_gain MAX_GAIN]
                             [--treble_f_lower TREBLE_F_LOWER]
                             [--treble_f_upper TREBLE_F_UPPER]
                             [--treble_max_gain TREBLE_MAX_GAIN]
                             [--treble_gain_k TREBLE_GAIN_K] [--show_plot]

optional arguments:
  -h, --help            show this help message and exit
  --input_dir INPUT_DIR
                        Path to input data directory. Will look for CSV files
                        in the data directory and recursively in sub-
                        directories.
  --output_dir OUTPUT_DIR
                        Path to results directory. Will keep the same relative
                        paths for files foundin input_dir.
  --calibration CALIBRATION
                        File path to CSV containing calibration data. See
                        `calibration` directory.
  --compensation COMPENSATION
                        File path to CSV containing compensation curve.
                        Compensation is necessary when equalizing because all
                        input data is raw microphone data. See
                        innerfidelity/resources and headphonecom/resources.
                        Defaults to "innerfidelity\resources\innerfidelity_com
                        pensation_SBAF-Serious.brighter.csv"
  --equalize            Will run equalization if this parameter exists, no
                        value needed.
  --bass_boost BASS_BOOST
                        Target gain for sub-bass in dB. Has flat response from
                        20 Hz to 60 Hz and a sigmoid slope down to 200 Hz.
                        Defaults to 0.0
  --tilt TILT           Target tilt in dB/octave. Positive value (upwards
                        slope) will result in brighter frequency response and
                        negative value (downwards slope) will result in darker
                        frequency response. 1 dB/octave will produce nearly 10
                        dB difference in desired value between 20 Hz and 20
                        kHz. Tilt is applied with bass boost and both will
                        affect the bass gain. Defaults to 0.0
  --max_gain MAX_GAIN   Maximum positive gain in equalization. Higher max gain
                        allows to equalize deeper dips in frequency response
                        but will limit output volume if no analog gain is
                        available because positive gain requires negative
                        digital preamp equal to maximum positive gain.
                        Defaults to 6.0
  --treble_f_lower TREBLE_F_LOWER
                        Lower bound for transition region between normal and
                        treble frequencies. Treble frequencies can have
                        different smoothing, max gain and gain K. Defaults to
                        6000.0
  --treble_f_upper TREBLE_F_UPPER
                        Upper bound for transition region between normal and
                        treble frequencies. Treble frequencies can have
                        different smoothing, max gain and gain K. Defaults to
                        8000.0
  --treble_max_gain TREBLE_MAX_GAIN
                        Maximum positive gain for equalization in treble
                        region. Defaults to 0.0
  --treble_gain_k TREBLE_GAIN_K
                        Coefficient for treble gain, affects both positive and
                        negative gain. Useful for disabling or reducing
                        equalization power in treble region. Defaults to 1.0.
  --show_plot           Plot will be shown if this parameter exists, no value
                        needed.

````

#### Examples
Equalizing Sennheiser HD 650 and saving results to `myresults/HD650`:
````commandline
python frequency_response.py --input_dir="innerfidelity\data\onear\Sennheiser HD 650" --output_dir="myresults\HD650" --compensation="innerfidelity\resources\innerfidelity_compensation_SBAF-Serious-brighter.csv" --equalize --bass_boost=4 --show_plot
````

Equalizing Beyerdynamic DT990 without saving results
````commandline
python frequency_response.py --input_dir="headphonecom\data\onear\Beyerdynamic DT990" --compensation="headphonecom\resources\headphonecom_compensation.csv" --equalize --bass_boost=4 --show_plot
````

Equalizing Beyerdynamic DT990 to SBAF-Serious-brighter target. This target is made for Innerfidelity measurements so
we need to calibrate Headphone.com measurement.
````commandline
python frequency_response.py --input_dir="headphonecom\data\onear\Beyerdynamic DT990" --compensation="innerfidelity\resources\innerfidelity_compensation_SBAF-Serious-brighter.csv" --calibration="calibration\headphonecom_raw_to_innerfidelity_raw.csv" --equalize --bass_boost=4 --show_plot
````

Equalizing all Headphone.com on-ear headphones and saving results to `results\onear\SBAF-Serious-brighter\headphonecom`.
There is a lot of headphones and we don't want to inspect all visually so we'll omit `--show_plot`
````commandline
python frequency_response.py --input_dir="headphonecom\data\onear" --output_dir="results\onear\SBAF-Serious-brighter\headphonecom" --compensation="innerfidelity\resources\innerfidelity_compensation_SBAF-Serious-brighter.csv" --calibration="calibration\headphonecom_raw_to_innerfidelity_raw.csv" --equalize --bass_boost=4
````

Equalizing Beyerdynamic DT 770 to sound like HiFiMAN HE400S. 80ohm version of DT 770 is only available in Headphone.com
measurements and HE400S only in Innerfidelity measurements so we'll use calibration once again. To make the bass sound
the same we'll set bass boost to zero.
````commandline
python frequency_response.py --input_dir="headphonecom\data\onear\Beyerdynamic DT770" --compensation="innerfidelity\data\onear\HiFiMAN HE400S\HiFiMAN HE400S.csv" --calibration="calibration\headphonecom_raw_to_innerfidelity_raw.csv" --equalize --bass_boost=0 --show_plot
````

Viewing HiFiMAN HE400S raw microphone data
````commandline
python frequency_response.py --input_dir="innerfidelity\data\onear\HiFiMAN HE400S" --show_plot
````

#### More Data!
If data for you headphone cannot be found in this project but you have an image of the frequency response you might be
able to use [https://apps.automeris.io/wpd/](https://apps.automeris.io/wpd/) to parse the image. You'll have replace
all commas `,` in the produced file with point `.` and semi-colons `;` with comma `,` in this order. You can you for
example [Notepad++](https://notepad-plus-plus.org/) for this, just hit `Ctrl+h`

### Calibration
- raw - calibration
- Same headphone models selected from both
- Difference in error calculated. Errors obtained by respective compensation curves.
- Calibration data is to be subtracted from error data (compensated data)
- Calibrating raw microphone data makes no sense because systems are different

### Data Processing
- Not meant as a user friendly tool. Obtaining data needs to be done only once.
- Innerfidelity
    - Innerfidelity PDFs crawled. PDFs turned into images. Images turned into data. All inspected manually.
    - Read data is the red and blue curves which are compensated with old compensation curve
    - Compensation curve 2016 obtained from Innerfidelity image
    - Compensation curve 2017 obtained from Innerfidelity image (see article...)
    - Transformation curve made from compensation curves
    - Data turned into raw data by adding compensation curve 2016
    - Original data still saved
- Headphone.com
    - Raw data images crawled. Compensated data images crawled. Images turned into data. All inspected manually.
    - Raw and compensated data used to produce compensation curve.
    - Only raw data kept.
  
### Technical Challenges
- Smoothing
- Interpolating
- Max gain with smoothed corners
