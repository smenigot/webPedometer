# Pedometer in HTML5 for Firefox OS and Firefox for Android

Pedometer made in HTML5. Run the script index.html.

## Introduction
<p>Nowadays,the connected devices are increasingly present. They are especially the "stars" of the latest <a href="http://www.cesweb.org/">Consumer Electronics Show</a> 2014 (Las Vegas, NV, USA) . Among these devices, there is one who knows a revival: pedometer. For a long time, pedometer has established in hiking field. But it has suffered in comparison with  geolocation which can offers greater simplicity (no setting) and greater accuracy in distance measurements. However, geolocation can be a complement to the pedometer since only the pedometer can measure the number of steps. With the accession of smartphones, all these measures can easily be performed with the same device. For example, there is a version for Android pedometer under GNU GPLv3 license [<a href="#ref1">1</a>].</p>
<p>However, it seems that the future of mobile technologies tends to turn to web technologies based on HTML5. Two projects of operating system arouse: <a href="https://www.mozilla.org/en-US/firefox/os/">Firefox OS</a> developed by the Mozilla Foundation and <a href="https://www.tizen.org">Tizen</a> developed by Linux Foundation. Note that web scripts can be added to programs in C++ through <a href="http://qt-project.org/doc/qt-4.8/qdeclarativejavascript.html">Qt Library</a>. These factors led me to develop a pedometer in HTML5 under the GNU GPLv3 License.</p>
<p>The recent development of new functionality enables to get information from gyroscopes easily. The web browser will calculate the acceleration in three smartphone axes x, y and z. From these three signals, when a person has a smartphone in the pocket, the steps can be detected. This web application is based on measures of digital accelerometer [<a href="#ref2">2</a>].</p>

<p style="text-align: center;"><a href="https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Orientation_and_motion_data_explained">![Alt text](img/axes.png" border="0" alt="axes of smartphone" title="axes of smartphone" width="265" height="247" /></a><span style="font-size: xx-small;"> </span></p>
<p><span style="font-size: xx-small;"><a href="https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Orientation_and_motion_data_explained"> </a></span></p>
<p style="text-align: center;"><span style="font-size: xx-small;"><a href="https://developer.mozilla.org/en-US/docs/Web/Guide/Events/Orientation_and_motion_data_explained">Orientation and motion data explained</a><br />Author: Sheppy (Mozilla Contributor).<br /><a href="http://creativecommons.org/licenses/by-sa/2.5/">Creative Commons Attribution-ShareAlike</a> license (CC-BY-SA)</span></p>
<p>The web application has been tested on a Geeksphone Peak (Madrid, Spain) with Firefox OS 1.3 Prerelase. Note that Firefox for Android, the web application works but no benchmark has been achieved in this platform.</p>

## Acceleration and Walking

<p>The measurement principle is described in the block diagram given in the following figure.</p>
<p style="text-align: center;">![Alt text](img/Diagramme1.png)</p>
<p>When a person walks, the smartphone in his pocket undergoes accelerations and decelerations. A step corresponds to a pseudo-period of the signal with acceleration variations. Counting the number of steps is therefore to increment a counter for pseudo-period performed.</p>
<p> </p>
<p style="text-align: center;"><img src="img/acc_xyz.png" border="0" alt="acceleration on 3 axes" title="acceleration on 3 axes" width="512" height="383" /></p>
<p>The first step is to merge information of the three accelerations d²x/dt², d²y/dt² and d²z/dt². The other advantage of this operation is to eliminate the absence of information on the position in the pocket, because the acceleration during walking can not be predicted. The simplest fusion is to compute the norm of the vector acceleration:</p>
<p style="text-align: center;"><img src="img/eq1.png" border="0" alt="eq norm" title="eq norm" width="372" height="64" style="border: 0px none;" /></p>
<p>where <em>t</em> is the discrete time.</p>
<p>To improve the robustness of detection, the steps are not directly counted on the standard acceleration <em>a(t)</em>. A denoising step is applied from a Kalman filter [<a>3</a>] by using the following equations.</p>
<p style="text-align: center;"><img src="img/eq2.png" border="0" alt="eq kalman gain" title="eq kalman gain" width="271" height="32" style="border: 0px none; vertical-align: middle;" /> (Optimal Kalman gain)<br /><img src="img/eq3.png" border="0" alt="eq Kalman" title="eq Kalman" width="255" height="32" style="border: 0px none; vertical-align: middle;" /> (Updated acceleration estimated)<br /><img src="img/eq4.png" border="0" alt="eq Kalman" title="eq Kalman" width="363" height="32" style="border: 0px none; vertical-align: middle;" /> (Updated covariance estimated)</p>
<p>where <img src="img/eq5.png" border="0" alt="P_t" title="P_t" width="39" height="16" /> is the error covariance matrix,  <img src="img/eq6.png" border="0" alt="a_t" width="20" height="16" /> the signal estimated at time t, <img src="img/eq7.png" border="0" alt="H_t" title="H_t" width="21" height="16" /> the matrix of the estimation model and  <img src="img/eq8.png" border="0" alt="R_t" title="R_t" width="20" height="16" /> the covariance of the noise mesured. Note that all parameters are initialized at 1 at the beginning of the computation.</p>
<p>This filter could be applied before the norm calculation on each three accelerations  d²x/dt², d²y/dt² and d²z/dt². However by applying the Kalman filter on the acceleration <em>a(t)</em>, the computation cost can be reduced. In this case, the dimension of the matrix are therefore one. Finally, we hypothesized that the signal without noise is sinusoidal with a variance of 0.5. By measuring the variance of acceleration a(t), we can deduce the noise variation.</p>
<p>The detection is made ​​on this denoised signal. A step is then detected if the acceleration cut an adaptive threshold. This threshold corresponds to the average of the extreme ​​acceleration values on a window of 2 seconds. Several conditions are also added to reduce false positives. First, it is necessary that the difference between the extrema exceeds to the sensitivity. This sensitivity is the double of the noise variance normalized. Moreover, to count a single step per pseudo-period, a condition on the derivative is added: the detection is performed only when the acceleration exceeding the threshold falls below this threshold. Finally, to reduce the risk of detecting false quick step, the minimum time between two step detection has to be 200 ms (13.6 km/h).</p>
<p> </p>
<p style="text-align: center;"><img src="img/acc_detect.png" border="0" alt="Step detection" title="Step detection" width="512" height="427" /></p>
<p>From these measurements, we deduce the instantaneous speed as an average speed on a window of 2 seconds. The calories burned are therefore computed [<a href="#ref2">2</a>]:</p>
<h2>Web Interface</h2>
<p style="text-align: center;"><img src="img/capture1.png" border="0" alt="Main page" title="Main page" width="360" height="640" /><img src="img/capture2.png" border="0" alt="Setting page" title="Setting page" width="360" height="640" /><img src="img/capture3.png" border="0" alt="Menu" title="Menu" width="360" height="640" /></p>
<p>The main page shows the step number, the distance walked, mean and instantaneous speed and calories burned. In the current version, the lock screen does not launch in Firefox OS. When this application is running, the lock screen is automatically locked.</p>
<p>From the menu, you can adjust the step size to determine the total distance traveled. We recommend counting your steps on a large known distance. The relationship between distance and the number of steps will give you the size of your steps. It is also possible to adjust the weight to calculate calories burned.</p>
<p>Finally you can also have a description of the application.</p>

## Conclusion

<p>This first version of the pedometer works on my Geeksphone Peak. It demonstrates the capabilities of real-time processing signal for an HTML5 application.</p>

## Version Note

<ul>
<li>Version 1.0.3 : first public version. It can count step, speed and burned calories from accelerometers.</li>
<li>Version 2.0.1 : the distance walked and the instantaneous speed can be checked from the  GPS data to increase the precision. However, it is just an option so  that the pedometer can continue to run even when GPS reception is poor. This option is activated by default. To activate it, go to the parameter menu, check the option and return to the main page.</li>
<li>Version 2.1 : Add of a "pause" button" on the main page.</li>
</ul>
<h2>Links and Download</h2>
<ul>
<li><a href="https://marketplace.firefox.com/app/podometre/">Web application on the Mozilla Marketplace</a> available in english, in french and partially in spanish</li>
<li><a href="webapp/pedometer/">Online web application</a> available in english, in french and partially in spanish</li>
<li><a href="webapp/pedometer/application-v2.1.zip">Development Code of the web application (version 2.1)</a> 
</li>
</ul>

## Confidentiality

<p>The application does not require an internet connection. Your statistics is recorded on your smartphone. No data is transferred.</p>
<p>The application is free under the <a href="https://www.gnu.org/licenses/licenses.html">GNU GPL v3 License</a>. Some javascript library are on MIT Licence and WTFPL Licence.</p>

## References

<p>[1] Pedometer for Android : <a href="https://code.google.com/p/pedometer/">https://code.google.com/p/pedometer/</a></p>
<p>[2] Zhao, N. <a href="http://www.analog.com/library/analogdialogue/archives/44-06/pedometer.html">Full-featured pedometer design realized with 3-Axis digital accelerometer</a>. Analog Dialogue, 2010, vol. 44, no 6, p. 1-5.</p>
<p>[3] Kalman, R. E. <a href="http://dx.doi.org/10.1115/1.3662552">A New Approach to Linear Filtering and Prediction Problems.</a> Journal of basic Engineering, 1960, vol 82 (Series D), no 1, p. 35-45.</p>
