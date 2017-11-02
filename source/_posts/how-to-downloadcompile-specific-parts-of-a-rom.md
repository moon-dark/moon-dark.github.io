---
title: How to Download/Compile Specific Parts of a ROM
tags:
  - Android
  - 源码
  - code
date: 2017-05-02 15:13:37
---

# [How to Download/Compile Specific Parts of a ROM](http://xda-university.com/as-a-developer/downloadcompile-specific-rom-parts)
<!--more-->
<div class="post-entry">

This is a tutorial about how to download and compile specific parts in <span class="explanatory-dictionary-highlight" data-definition="explanatory-dictionary-definition-0">AOSP</span> based roms (such as CyanogenMod, AOKP, SlimRom etc)
It’s mainly dedicated for the people who want to get more familiar with the <span class="explanatory-dictionary-highlight" data-definition="explanatory-dictionary-definition-0">AOSP</span> build environment, and would like to manipulate it’s behavior to complete specific tasks.

**Introduction:**
Downloading <span class="explanatory-dictionary-highlight" data-definition="explanatory-dictionary-definition-16">ROM</span> sources is done with the tool “<span class="explanatory-dictionary-highlight" data-definition="explanatory-dictionary-definition-2">repo</span>”, this is the most awesome downloader in existence. Not only it looks very cool, it also reaches top speed at all times. But it’s only for linux (which is good, since when you are busy with <span class="explanatory-dictionary-highlight" data-definition="explanatory-dictionary-definition-0">aosp</span> roms, you should be on linux!)
Why is it awesome? Since you decide yourself how many jobs (aka connections) you make with the server. This means you always will get top speed, there is probably no faster download method available on the Internet (except for speedtests, but they don’t count). Next to the downloading of specific parts it’s of course also handy to know how to actually compile them.

&nbsp;

**Chapters:**
**1\. Download
2\. Compile **
**3\. Automate **(optional)

&nbsp;

**Chapter 1\. ****Download:**
The easiest way of fetching for example a batch of <span class="explanatory-dictionary-highlight" data-definition="explanatory-dictionary-definition-0">AOSP</span> apps is like this:

1\. Choose the desired [tag](https://android.googlesource.com/platform/manifest/+refs) you want to use, in the example I will use android-4.2.2_r1
2\. Now make a dir somewhere:
<div>
<div id="crayon-59082d1e8a7f3997183607" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
<div class="crayon-main">
<table class="crayon-table">
<tbody>
<tr class="crayon-row">
<td class="crayon-nums " data-settings="show">
<div class="crayon-nums-content">
<div class="crayon-num" data-line="crayon-59082d1e8a7f3997183607-1">1</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a7f3997183607-2">2</div>
</div></td>
<td class="crayon-code">
<div class="crayon-pre">
<div id="crayon-59082d1e8a7f3997183607-1" class="crayon-line"><span class="crayon-v">mkdir</span> <span class="crayon-o">~</span><span class="crayon-o">/</span><span class="crayon-v">aosp</span><span class="crayon-o">-</span><span class="crayon-e">apps</span></div>
<div id="crayon-59082d1e8a7f3997183607-2" class="crayon-line crayon-striped-line"><span class="crayon-v">cd</span> <span class="crayon-o">~</span><span class="crayon-o">/</span><span class="crayon-v">aosp</span><span class="crayon-o">-</span><span class="crayon-v">apps</span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
3\. Download <span class="explanatory-dictionary-highlight" data-definition="explanatory-dictionary-definition-2">repo</span> when not installed yet:
<div>
<div id="crayon-59082d1e8a7fe185536749" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
<div class="crayon-main">
<table class="crayon-table">
<tbody>
<tr class="crayon-row">
<td class="crayon-nums " data-settings="show">
<div class="crayon-nums-content">
<div class="crayon-num" data-line="crayon-59082d1e8a7fe185536749-1">1</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a7fe185536749-2">2</div>
</div></td>
<td class="crayon-code">
<div class="crayon-pre">
<div id="crayon-59082d1e8a7fe185536749-1" class="crayon-line"><span class="crayon-e">curl </span><span class="crayon-v">https</span><span class="crayon-o">:</span><span class="crayon-c">//dl-ssl.google.com/dl/googlesource/git-repo/repo &gt; ~/bin/repo</span></div>
<div id="crayon-59082d1e8a7fe185536749-2" class="crayon-line crayon-striped-line"><span class="crayon-i">chmod</span> <span class="crayon-v">a</span><span class="crayon-o">+</span><span class="crayon-v">x</span> <span class="crayon-o">~</span><span class="crayon-o">/</span><span class="crayon-v">bin</span><span class="crayon-o">/</span><span class="crayon-v">repo</span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
4\. Enter the line that will download the initial <span class="explanatory-dictionary-highlight" data-definition="explanatory-dictionary-definition-24">manifest</span>.

_for <span class="explanatory-dictionary-highlight" data-definition="explanatory-dictionary-definition-0">AOSP</span>:_
<div>
<div id="crayon-59082d1e8a805801239022" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
<div class="crayon-main">
<table class="crayon-table">
<tbody>
<tr class="crayon-row">
<td class="crayon-nums " data-settings="show">
<div class="crayon-nums-content">
<div class="crayon-num" data-line="crayon-59082d1e8a805801239022-1">1</div>
</div></td>
<td class="crayon-code">
<div class="crayon-pre">
<div id="crayon-59082d1e8a805801239022-1" class="crayon-line"><span class="crayon-e">repo </span><span class="crayon-v">init</span> <span class="crayon-o">-</span><span class="crayon-i">u</span> <span class="crayon-v">https</span><span class="crayon-o">:</span><span class="crayon-c">//android.googlesource.com/platform/manifest -b android-4.2.2_r1</span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
Enter your name and email address and wait until it completes

_for CM:_
<div>
<div id="crayon-59082d1e8a80a992577825" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
<div class="crayon-main">
<table class="crayon-table">
<tbody>
<tr class="crayon-row">
<td class="crayon-nums " data-settings="show">
<div class="crayon-nums-content">
<div class="crayon-num" data-line="crayon-59082d1e8a80a992577825-1">1</div>
</div></td>
<td class="crayon-code">
<div class="crayon-pre">
<div id="crayon-59082d1e8a80a992577825-1" class="crayon-line"><span class="crayon-e">repo </span><span class="crayon-v">init</span> <span class="crayon-o">-</span><span class="crayon-i">u</span> <span class="crayon-v">https</span><span class="crayon-o">:</span><span class="crayon-c">//github.com/Cyanogenmod/android -b cm-10.1</span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
5\. Go in the hidden “.<span class="explanatory-dictionary-highlight" data-definition="explanatory-dictionary-definition-2">repo</span>” dir and change the <span class="explanatory-dictionary-highlight" data-definition="explanatory-dictionary-definition-24">manifest</span>
<div>
<div id="crayon-59082d1e8a80f824499008" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
<div class="crayon-main">
<table class="crayon-table">
<tbody>
<tr class="crayon-row">
<td class="crayon-nums " data-settings="show">
<div class="crayon-nums-content">
<div class="crayon-num" data-line="crayon-59082d1e8a80f824499008-1">1</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a80f824499008-2">2</div>
</div></td>
<td class="crayon-code">
<div class="crayon-pre">
<div id="crayon-59082d1e8a80f824499008-1" class="crayon-line"><span class="crayon-i">cd</span> <span class="crayon-sy">.</span><span class="crayon-e">repo</span></div>
<div id="crayon-59082d1e8a80f824499008-2" class="crayon-line crayon-striped-line"><span class="crayon-e">gedit </span><span class="crayon-v">manifest</span><span class="crayon-sy">.</span><span class="crayon-v">xml</span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
_If you don’t have decent text editor such as “gedit”, install it! “sudo apt-get install gedit”_

&nbsp;

For example when you only want to download the apps, just remove all lines that are not app related. simple as that
<div>
<div id="crayon-59082d1e8a815016976861" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
<div class="crayon-main">
<table class="crayon-table">
<tbody>
<tr class="crayon-row">
<td class="crayon-nums " data-settings="show">
<div class="crayon-nums-content">
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-1">1</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-2">2</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-3">3</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-4">4</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-5">5</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-6">6</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-7">7</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-8">8</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-9">9</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-10">10</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-11">11</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-12">12</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-13">13</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-14">14</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-15">15</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-16">16</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-17">17</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-18">18</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-19">19</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-20">20</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-21">21</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-22">22</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-23">23</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-24">24</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-25">25</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-26">26</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-27">27</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-28">28</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-29">29</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-30">30</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-31">31</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-32">32</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-33">33</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-34">34</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-35">35</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-36">36</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-37">37</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-38">38</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-39">39</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-40">40</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-41">41</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-42">42</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-43">43</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a815016976861-44">44</div>
<div class="crayon-num" data-line="crayon-59082d1e8a815016976861-45">45</div>
</div></td>
<td class="crayon-code">
<div class="crayon-pre">
<div id="crayon-59082d1e8a815016976861-1" class="crayon-line"><span class="crayon-ta">&lt;?</span><span class="crayon-e">xml </span><span class="crayon-i">version</span><span class="crayon-o">=</span>”<span class="crayon-cn">1.0</span>″ <span class="crayon-i">encoding</span><span class="crayon-o">=</span>”<span class="crayon-cn">UTF</span><span class="crayon-o">-</span><span class="crayon-cn">8</span>″<span class="crayon-ta">?&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-2" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-v">manifest</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-3" class="crayon-line"></div>
<div id="crayon-59082d1e8a815016976861-4" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">remote  </span><span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-i">aosp</span>”</div>
<div id="crayon-59082d1e8a815016976861-5" class="crayon-line"><span class="crayon-h">    </span><span class="crayon-v">fetch</span><span class="crayon-o">=</span>”<span class="crayon-sy">.</span><span class="crayon-sy">.</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-6" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-st">default</span> <span class="crayon-v">revision</span><span class="crayon-o">=</span>”<span class="crayon-v">refs</span><span class="crayon-o">/</span><span class="crayon-v">tags</span><span class="crayon-o">/</span><span class="crayon-v">android</span><span class="crayon-o">-</span><span class="crayon-cn">4.2.2_r1</span>″</div>
<div id="crayon-59082d1e8a815016976861-7" class="crayon-line"><span class="crayon-h">    </span><span class="crayon-v">remote</span><span class="crayon-o">=</span>”<span class="crayon-i">aosp</span>”</div>
<div id="crayon-59082d1e8a815016976861-8" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-v">sync</span><span class="crayon-o">-</span><span class="crayon-v">j</span><span class="crayon-o">=</span>”<span class="crayon-cn">4</span>″ <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-9" class="crayon-line"></div>
<div id="crayon-59082d1e8a815016976861-10" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">BasicSmsReceiver</span>”<span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">BasicSmsReceiver</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-11" class="crayon-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Bluetooth</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Bluetooth</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-12" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Browser</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Browser</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-13" class="crayon-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Calculator</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Calculator</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-14" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Calendar</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Calendar</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-15" class="crayon-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Camera</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Camera</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-16" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">CellBroadcastReceiver</span>”<span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">CellBroadcastReceiver</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-17" class="crayon-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">CertInstaller</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">CertInstaller</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-18" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Contacts</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Contacts</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-19" class="crayon-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">DeskClock</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">DeskClock</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-20" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Email</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Email</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-21" class="crayon-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Exchange</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Exchange</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-22" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Gallery</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Gallery</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-23" class="crayon-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Gallery2</span>″ <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Gallery2</span>″ <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-24" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">HTMLViewer</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">HTMLViewer</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-25" class="crayon-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">KeyChain</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">KeyChain</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-26" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Launcher2</span>″ <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Launcher2</span>″ <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-27" class="crayon-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">LegacyCamera</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">LegacyCamera</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-28" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Mms</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Mms</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-29" class="crayon-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Music</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Music</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-30" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">MusicFX</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">MusicFX</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-31" class="crayon-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Nfc</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Nfc</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-32" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">PackageInstaller</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">PackageInstaller</span>”<span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-33" class="crayon-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Phone</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Phone</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-34" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Protips</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Protips</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-35" class="crayon-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Provision</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Provision</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-36" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">QuickSearchBox</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">QuickSearchBox</span>”<span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-37" class="crayon-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Settings</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Settings</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-38" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">SoundRecorder</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">SoundRecorder</span>”<span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-39" class="crayon-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">SpareParts</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">SpareParts</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-40" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">SpeechRecorder</span>”<span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">SpeechRecorder</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-41" class="crayon-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Stk</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Stk</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-42" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Tag</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">Tag</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-43" class="crayon-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">VideoEditor</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">VideoEditor</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-44" class="crayon-line crayon-striped-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-e">project </span><span class="crayon-v">path</span><span class="crayon-o">=</span>”<span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">VoiceDialer</span>” <span class="crayon-v">name</span><span class="crayon-o">=</span>”<span class="crayon-v">platform</span><span class="crayon-o">/</span><span class="crayon-v">packages</span><span class="crayon-o">/</span><span class="crayon-v">apps</span><span class="crayon-o">/</span><span class="crayon-i">VoiceDialer</span>” <span class="crayon-o">/</span><span class="crayon-o">&gt;</span></div>
<div id="crayon-59082d1e8a815016976861-45" class="crayon-line"><span class="crayon-h">    </span><span class="crayon-o">&lt;</span><span class="crayon-o">/</span><span class="crayon-v">manifest</span><span class="crayon-o">&gt;</span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
When done, save it and go a dir back in the terminal
<div>
<div id="crayon-59082d1e8a81d896283046" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
<div class="crayon-main">
<table class="crayon-table">
<tbody>
<tr class="crayon-row">
<td class="crayon-nums " data-settings="show">
<div class="crayon-nums-content">
<div class="crayon-num" data-line="crayon-59082d1e8a81d896283046-1">1</div>
</div></td>
<td class="crayon-code">
<div class="crayon-pre">
<div id="crayon-59082d1e8a81d896283046-1" class="crayon-line"><span class="crayon-i">cd</span> <span class="crayon-sy">.</span><span class="crayon-sy">.</span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
6\. Download the packages
<div>
<div id="crayon-59082d1e8a822938912766" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
<div class="crayon-main">
<table class="crayon-table">
<tbody>
<tr class="crayon-row">
<td class="crayon-nums " data-settings="show">
<div class="crayon-nums-content">
<div class="crayon-num" data-line="crayon-59082d1e8a822938912766-1">1</div>
</div></td>
<td class="crayon-code">
<div class="crayon-pre">
<div id="crayon-59082d1e8a822938912766-1" class="crayon-line"><span class="crayon-e">repo </span><span class="crayon-v">sync</span> <span class="crayon-o">-</span><span class="crayon-e">j16</span><span class="crayon-h">              </span><span class="crayon-sy">(</span><span class="crayon-o">-</span><span class="crayon-e">j16 </span><span class="crayon-e">stands </span><span class="crayon-st">for</span> <span class="crayon-cn">16</span> <span class="crayon-e">jobs </span><span class="crayon-v">max</span><span class="crayon-sy">.</span> <span class="crayon-r">This</span> <span class="crayon-st">is</span> <span class="crayon-st">for</span> <span class="crayon-i">around</span> <span class="crayon-i">a</span> <span class="crayon-cn">100mbit</span> <span class="crayon-v">line</span><span class="crayon-sy">)</span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
(With a 60mb/s connection you already get the max download speed with ” -j4 ” which is the default of github)

The packages will be download as quickly as possible to your harddrive now.

&nbsp;

**Chapter 2\. ****Compile:**
To compile the obtained packages separately you will need a <span class="explanatory-dictionary-highlight" data-definition="explanatory-dictionary-definition-0">AOSP</span> environment (or CM/AOKP etc). When you have a succesfull environment which is able to compile for your device you may enter:
<div>
<div id="crayon-59082d1e8a828163248475" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
<div class="crayon-main">
<table class="crayon-table">
<tbody>
<tr class="crayon-row">
<td class="crayon-nums " data-settings="show">
<div class="crayon-nums-content">
<div class="crayon-num" data-line="crayon-59082d1e8a828163248475-1">1</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a828163248475-2">2</div>
</div></td>
<td class="crayon-code">
<div class="crayon-pre">
<div id="crayon-59082d1e8a828163248475-1" class="crayon-line"><span class="crayon-e">source </span><span class="crayon-v">build</span><span class="crayon-o">/</span><span class="crayon-v">envsetup</span><span class="crayon-sy">.</span><span class="crayon-e">sh</span></div>
<div id="crayon-59082d1e8a828163248475-2" class="crayon-line crayon-striped-line"><span class="crayon-e">lunch</span> <span class="crayon-sy">(</span><span class="crayon-e">now </span><span class="crayon-e">choose </span><span class="crayon-e">your </span><span class="crayon-v">device</span><span class="crayon-sy">,</span> <span class="crayon-st">for</span> <span class="crayon-v">example</span><span class="crayon-sy">,</span> <span class="crayon-v">full_ariesve</span><span class="crayon-o">-</span><span class="crayon-v">userdebug</span><span class="crayon-sy">)</span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
now you’re theoretically ready to compile
to compile an app on the easiest way:
<div>
<div id="crayon-59082d1e8a82d286852766" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
<div class="crayon-main">
<table class="crayon-table">
<tbody>
<tr class="crayon-row">
<td class="crayon-nums " data-settings="show">
<div class="crayon-nums-content">
<div class="crayon-num" data-line="crayon-59082d1e8a82d286852766-1">1</div>
</div></td>
<td class="crayon-code">
<div class="crayon-pre">
<div id="crayon-59082d1e8a82d286852766-1" class="crayon-line"><span class="crayon-e">make </span><span class="crayon-v">Appname</span> <span class="crayon-o">-</span><span class="crayon-v">j8</span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
where “Appname” is the name of the folder placed in packages/apps/..
This also counts for “SystemUI” which is not in that folder
<div>
<div id="crayon-59082d1e8a832619347880" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
<div class="crayon-main">
<table class="crayon-table">
<tbody>
<tr class="crayon-row">
<td class="crayon-nums " data-settings="show">
<div class="crayon-nums-content">
<div class="crayon-num" data-line="crayon-59082d1e8a832619347880-1">1</div>
</div></td>
<td class="crayon-code">
<div class="crayon-pre">
<div id="crayon-59082d1e8a832619347880-1" class="crayon-line"><span class="crayon-e">make </span><span class="crayon-v">Calculator</span> <span class="crayon-o">-</span><span class="crayon-e">j8</span> <span class="crayon-sy">(</span><span class="crayon-st">for</span> <span class="crayon-v">example</span><span class="crayon-sy">)</span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
And the -j param again stands for the number of jobs, remember that this is compiling and not downloading, so the “standard rule” with this is, number of cores of your CPU + 1.
Which makes a regular quad core (-j5) but makes a i7 CPU with Hyper threading (-j9). When using -j16 for example on a regular quad core it could happen that your CPU will be overloaded so much that your mouse will lag all over the place.
(Linux forces the cpu to perform higher then it actually should, resulting in unwanted situations)

When having trouble, when it stops or just nags about some missing symbols or something like that.
You should try using the “-k” param, this tells the make command to keep going when a non-critical error occurs.
<div>
<div id="crayon-59082d1e8a838456332771" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
<div class="crayon-main">
<table class="crayon-table">
<tbody>
<tr class="crayon-row">
<td class="crayon-nums " data-settings="show">
<div class="crayon-nums-content">
<div class="crayon-num" data-line="crayon-59082d1e8a838456332771-1">1</div>
</div></td>
<td class="crayon-code">
<div class="crayon-pre">
<div id="crayon-59082d1e8a838456332771-1" class="crayon-line"><span class="crayon-e">make </span><span class="crayon-v">Calculator</span> <span class="crayon-o">-</span><span class="crayon-v">k</span> <span class="crayon-o">-</span><span class="crayon-v">j8</span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
When having issues with the local module tags, you should enter this:
<div>
<div id="crayon-59082d1e8a83d007945517" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
<div class="crayon-main">
<table class="crayon-table">
<tbody>
<tr class="crayon-row">
<td class="crayon-nums " data-settings="show">
<div class="crayon-nums-content">
<div class="crayon-num" data-line="crayon-59082d1e8a83d007945517-1">1</div>
</div></td>
<td class="crayon-code">
<div class="crayon-pre">
<div id="crayon-59082d1e8a83d007945517-1" class="crayon-line"><span class="crayon-e">make </span><span class="crayon-v">Calculator</span> <span class="crayon-o">-</span><span class="crayon-i">k</span> <span class="crayon-v">LOCAL_MODULE_TAGS</span><span class="crayon-o">=</span><span class="crayon-v">optional</span> <span class="crayon-o">-</span><span class="crayon-v">j8</span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
To compile other parts you also need to enter the folder name, here are some examples from /frameworks/base
<div>
<div id="crayon-59082d1e8a842937855866" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
<div class="crayon-main">
<table class="crayon-table">
<tbody>
<tr class="crayon-row">
<td class="crayon-nums " data-settings="show">
<div class="crayon-nums-content">
<div class="crayon-num" data-line="crayon-59082d1e8a842937855866-1">1</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a842937855866-2">2</div>
<div class="crayon-num" data-line="crayon-59082d1e8a842937855866-3">3</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a842937855866-4">4</div>
</div></td>
<td class="crayon-code">
<div class="crayon-pre">
<div id="crayon-59082d1e8a842937855866-1" class="crayon-line"><span class="crayon-e">make </span><span class="crayon-v">android</span><span class="crayon-sy">.</span><span class="crayon-e">policy</span><span class="crayon-h">        </span><span class="crayon-sy">(</span><span class="crayon-e">the </span><span class="crayon-e">power </span><span class="crayon-e">menu </span><span class="crayon-st">and</span> <span class="crayon-v">lockscreen</span><span class="crayon-sy">)</span></div>
<div id="crayon-59082d1e8a842937855866-2" class="crayon-line crayon-striped-line"><span class="crayon-e">make </span><span class="crayon-e">framework</span><span class="crayon-h">             </span><span class="crayon-sy">(</span><span class="crayon-e">the </span><span class="crayon-e">initial </span><span class="crayon-e">framework </span><span class="crayon-v">files</span><span class="crayon-sy">)</span></div>
<div id="crayon-59082d1e8a842937855866-3" class="crayon-line"><span class="crayon-e">make </span><span class="crayon-v">framework</span><span class="crayon-o">-</span><span class="crayon-e">res</span><span class="crayon-h">         </span><span class="crayon-sy">(</span><span class="crayon-e">the </span><span class="crayon-e">initial </span><span class="crayon-e">framework </span><span class="crayon-v">resources</span><span class="crayon-sy">)</span></div>
<div id="crayon-59082d1e8a842937855866-4" class="crayon-line crayon-striped-line"><span class="crayon-e">make </span><span class="crayon-e">services</span><span class="crayon-h">              </span><span class="crayon-sy">(</span><span class="crayon-e">the </span><span class="crayon-v">services</span><span class="crayon-sy">.</span><span class="crayon-e">jar </span><span class="crayon-v">file</span><span class="crayon-sy">)</span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
or more examples:
<div>
<div id="crayon-59082d1e8a847598714388" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
<div class="crayon-main">
<table class="crayon-table">
<tbody>
<tr class="crayon-row">
<td class="crayon-nums " data-settings="show">
<div class="crayon-nums-content">
<div class="crayon-num" data-line="crayon-59082d1e8a847598714388-1">1</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a847598714388-2">2</div>
<div class="crayon-num" data-line="crayon-59082d1e8a847598714388-3">3</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a847598714388-4">4</div>
<div class="crayon-num" data-line="crayon-59082d1e8a847598714388-5">5</div>
</div></td>
<td class="crayon-code">
<div class="crayon-pre">
<div id="crayon-59082d1e8a847598714388-1" class="crayon-line"><span class="crayon-e">make </span><span class="crayon-e">sdk</span><span class="crayon-h">                   </span><span class="crayon-sy">(</span><span class="crayon-e">builds </span><span class="crayon-e">the </span><span class="crayon-e">android </span><span class="crayon-v">sdk</span><span class="crayon-sy">)</span></div>
<div id="crayon-59082d1e8a847598714388-2" class="crayon-line crayon-striped-line"><span class="crayon-e">make </span><span class="crayon-e">modules</span><span class="crayon-h">               </span><span class="crayon-sy">(</span><span class="crayon-e">builds </span><span class="crayon-e">all </span><span class="crayon-v">modules</span><span class="crayon-sy">)</span></div>
<div id="crayon-59082d1e8a847598714388-3" class="crayon-line"><span class="crayon-e">make </span><span class="crayon-e">installclean</span><span class="crayon-h">          </span><span class="crayon-sy">(</span><span class="crayon-e">removes </span><span class="crayon-e">all </span><span class="crayon-e">staging </span><span class="crayon-v">directories</span><span class="crayon-sy">,</span> <span class="crayon-e">such </span><span class="crayon-st">as</span> <span class="crayon-v">out</span><span class="crayon-o">/</span><span class="crayon-v">target</span><span class="crayon-o">/</span><span class="crayon-v">product</span><span class="crayon-o">/</span><span class="crayon-v">boardname</span><span class="crayon-o">/</span><span class="crayon-v">system</span><span class="crayon-sy">)</span></div>
<div id="crayon-59082d1e8a847598714388-4" class="crayon-line crayon-striped-line"><span class="crayon-e">make </span><span class="crayon-e">clean</span><span class="crayon-h">                 </span><span class="crayon-sy">(</span><span class="crayon-e">removes </span><span class="crayon-e">the </span><span class="crayon-v">whole</span> <span class="crayon-o">/</span><span class="crayon-e">out </span><span class="crayon-v">directory</span><span class="crayon-sy">)</span></div>
<div id="crayon-59082d1e8a847598714388-5" class="crayon-line"><span class="crayon-e">make </span><span class="crayon-e">recoveryimage</span><span class="crayon-h">         </span><span class="crayon-sy">(</span><span class="crayon-e">builds </span><span class="crayon-e">the </span><span class="crayon-e">recovery </span><span class="crayon-v">from</span> <span class="crayon-o">/</span><span class="crayon-v">bootable</span><span class="crayon-o">/</span><span class="crayon-v">recovery</span><span class="crayon-sy">,</span> <span class="crayon-e">customizable </span><span class="crayon-st">if</span> <span class="crayon-v">wanted</span><span class="crayon-o">!</span><span class="crayon-sy">)</span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
&nbsp;

**Chapter 3\. Automate**

If you regularly want to compile specific <span class="explanatory-dictionary-highlight" data-definition="explanatory-dictionary-definition-16">ROM</span> parts you can make a shell script that automates the process for you.

Make a new document with some name, for example “buildessentials”, open the file with your favorite text editor (gedit is recommended)
and place this line at the very top of your document:
<div id="crayon-59082d1e8a84d840627459" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
<div class="crayon-main">
<table class="crayon-table">
<tbody>
<tr class="crayon-row">
<td class="crayon-nums " data-settings="show">
<div class="crayon-nums-content">
<div class="crayon-num" data-line="crayon-59082d1e8a84d840627459-1">1</div>
</div></td>
<td class="crayon-code">
<div class="crayon-pre">
<div id="crayon-59082d1e8a84d840627459-1" class="crayon-line"><span class="crayon-p">#!/bin/bash</span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
This indicates that this file should call /bin/bash, which is the shell interpreter.

Under this line you should place all the single commands that you want to be executed when running the script. For example:
<div id="crayon-59082d1e8a852588626963" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
<div class="crayon-main">
<table class="crayon-table">
<tbody>
<tr class="crayon-row">
<td class="crayon-nums " data-settings="show">
<div class="crayon-nums-content">
<div class="crayon-num" data-line="crayon-59082d1e8a852588626963-1">1</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a852588626963-2">2</div>
<div class="crayon-num" data-line="crayon-59082d1e8a852588626963-3">3</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a852588626963-4">4</div>
<div class="crayon-num" data-line="crayon-59082d1e8a852588626963-5">5</div>
</div></td>
<td class="crayon-code">
<div class="crayon-pre">
<div id="crayon-59082d1e8a852588626963-1" class="crayon-line"><span class="crayon-sy">.</span> <span class="crayon-v">build</span><span class="crayon-o">/</span><span class="crayon-v">envsetup</span><span class="crayon-sy">.</span><span class="crayon-e">sh</span><span class="crayon-h">               </span><span class="crayon-sy">(</span><span class="crayon-e">inits </span><span class="crayon-e">the </span><span class="crayon-e">environement </span><span class="crayon-e">setup </span><span class="crayon-v">file</span><span class="crayon-sy">,</span> <span class="crayon-e">which </span><span class="crayon-st">is</span> <span class="crayon-e">always </span><span class="crayon-v">needed</span><span class="crayon-sy">)</span></div>
<div id="crayon-59082d1e8a852588626963-2" class="crayon-line crayon-striped-line"><span class="crayon-e">lunch </span><span class="crayon-v">full_ariesve</span><span class="crayon-o">-</span><span class="crayon-e">userdebug</span><span class="crayon-h">      </span><span class="crayon-sy">(</span><span class="crayon-e">chooses </span><span class="crayon-e">the </span><span class="crayon-e">wanted </span><span class="crayon-v">device</span><span class="crayon-sy">,</span> <span class="crayon-i">I</span> <span class="crayon-e">chose </span><span class="crayon-st">for</span> <span class="crayon-e">the </span><span class="crayon-v">i9001</span><span class="crayon-sy">,</span> <span class="crayon-e">which </span><span class="crayon-st">is</span> <span class="crayon-e">called </span><span class="crayon-v">ariesve</span><span class="crayon-sy">)</span></div>
<div id="crayon-59082d1e8a852588626963-3" class="crayon-line"><span class="crayon-e">make </span><span class="crayon-v">android</span><span class="crayon-sy">.</span><span class="crayon-v">policy</span> <span class="crayon-o">-</span><span class="crayon-e">j8</span><span class="crayon-h">           </span><span class="crayon-sy">(</span><span class="crayon-e">builds </span><span class="crayon-v">android</span><span class="crayon-sy">.</span><span class="crayon-v">policy</span><span class="crayon-sy">.</span><span class="crayon-v">jar</span><span class="crayon-sy">)</span></div>
<div id="crayon-59082d1e8a852588626963-4" class="crayon-line crayon-striped-line"><span class="crayon-e">make </span><span class="crayon-v">SystemUI</span> <span class="crayon-o">-</span><span class="crayon-e">j8</span><span class="crayon-h">                 </span><span class="crayon-sy">(</span><span class="crayon-e">builds </span><span class="crayon-v">SystemUI</span><span class="crayon-sy">.</span><span class="crayon-v">apk</span><span class="crayon-sy">)</span></div>
<div id="crayon-59082d1e8a852588626963-5" class="crayon-line"><span class="crayon-e">make </span><span class="crayon-v">recoveryimage</span> <span class="crayon-o">-</span><span class="crayon-e">j8</span><span class="crayon-h">            </span><span class="crayon-sy">(</span><span class="crayon-e">builds </span><span class="crayon-v">recovery</span><span class="crayon-sy">.</span><span class="crayon-v">img</span><span class="crayon-sy">)</span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
So your total output would be:
<div id="crayon-59082d1e8a858190930700" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
<div class="crayon-main">
<table class="crayon-table">
<tbody>
<tr class="crayon-row">
<td class="crayon-nums " data-settings="show">
<div class="crayon-nums-content">
<div class="crayon-num" data-line="crayon-59082d1e8a858190930700-1">1</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a858190930700-2">2</div>
<div class="crayon-num" data-line="crayon-59082d1e8a858190930700-3">3</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a858190930700-4">4</div>
<div class="crayon-num" data-line="crayon-59082d1e8a858190930700-5">5</div>
<div class="crayon-num crayon-striped-num" data-line="crayon-59082d1e8a858190930700-6">6</div>
</div></td>
<td class="crayon-code">
<div class="crayon-pre">
<div id="crayon-59082d1e8a858190930700-1" class="crayon-line"><span class="crayon-p">#!/bin/bash</span></div>
<div id="crayon-59082d1e8a858190930700-2" class="crayon-line crayon-striped-line"><span class="crayon-sy">.</span> <span class="crayon-v">build</span><span class="crayon-o">/</span><span class="crayon-v">envsetup</span><span class="crayon-sy">.</span><span class="crayon-e">sh</span><span class="crayon-h">               </span><span class="crayon-sy">(</span><span class="crayon-e">inits </span><span class="crayon-e">the </span><span class="crayon-e">environement </span><span class="crayon-e">setup </span><span class="crayon-v">file</span><span class="crayon-sy">,</span> <span class="crayon-e">which </span><span class="crayon-st">is</span> <span class="crayon-e">always </span><span class="crayon-v">needed</span><span class="crayon-sy">)</span></div>
<div id="crayon-59082d1e8a858190930700-3" class="crayon-line"><span class="crayon-e">lunch </span><span class="crayon-v">full_ariesve</span><span class="crayon-o">-</span><span class="crayon-e">userdebug</span><span class="crayon-h">      </span><span class="crayon-sy">(</span><span class="crayon-e">chooses </span><span class="crayon-e">the </span><span class="crayon-e">wanted </span><span class="crayon-v">device</span><span class="crayon-sy">,</span> <span class="crayon-i">I</span> <span class="crayon-e">chose </span><span class="crayon-st">for</span> <span class="crayon-e">the </span><span class="crayon-v">i9001</span><span class="crayon-sy">,</span> <span class="crayon-e">which </span><span class="crayon-st">is</span> <span class="crayon-e">called </span><span class="crayon-v">ariesve</span><span class="crayon-sy">)</span></div>
<div id="crayon-59082d1e8a858190930700-4" class="crayon-line crayon-striped-line"><span class="crayon-e">make </span><span class="crayon-v">android</span><span class="crayon-sy">.</span><span class="crayon-v">policy</span> <span class="crayon-o">-</span><span class="crayon-e">j8</span><span class="crayon-h">           </span><span class="crayon-sy">(</span><span class="crayon-e">builds </span><span class="crayon-v">android</span><span class="crayon-sy">.</span><span class="crayon-v">policy</span><span class="crayon-sy">.</span><span class="crayon-v">jar</span><span class="crayon-sy">)</span></div>
<div id="crayon-59082d1e8a858190930700-5" class="crayon-line"><span class="crayon-e">make </span><span class="crayon-v">SystemUI</span> <span class="crayon-o">-</span><span class="crayon-e">j8</span><span class="crayon-h">                 </span><span class="crayon-sy">(</span><span class="crayon-e">builds </span><span class="crayon-v">SystemUI</span><span class="crayon-sy">.</span><span class="crayon-v">apk</span><span class="crayon-sy">)</span></div>
<div id="crayon-59082d1e8a858190930700-6" class="crayon-line crayon-striped-line"><span class="crayon-e">make </span><span class="crayon-v">recoveryimage</span> <span class="crayon-o">-</span><span class="crayon-e">j8</span><span class="crayon-h">            </span><span class="crayon-sy">(</span><span class="crayon-e">builds </span><span class="crayon-v">recovery</span><span class="crayon-sy">.</span><span class="crayon-v">img</span><span class="crayon-sy">)</span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
Now you should change the permissions of the files, which need to be RWX-RX-RX, which is 755.
This can be done by entering this in the shell:
<div id="crayon-59082d1e8a85d270770760" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
<div class="crayon-main">
<table class="crayon-table">
<tbody>
<tr class="crayon-row">
<td class="crayon-nums " data-settings="show">
<div class="crayon-nums-content">
<div class="crayon-num" data-line="crayon-59082d1e8a85d270770760-1">1</div>
</div></td>
<td class="crayon-code">
<div class="crayon-pre">
<div id="crayon-59082d1e8a85d270770760-1" class="crayon-line"><span class="crayon-i">chmod</span> <span class="crayon-cn">755</span> <span class="crayon-v">buildessential</span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
Now you can run the script by entering:
<div id="crayon-59082d1e8a863138216292" class="crayon-syntax crayon-theme-classic crayon-font-monaco crayon-os-pc print-yes notranslate" data-settings=" minimize scroll-mouseover">
<div class="crayon-main">
<table class="crayon-table">
<tbody>
<tr class="crayon-row">
<td class="crayon-nums " data-settings="show">
<div class="crayon-nums-content">
<div class="crayon-num" data-line="crayon-59082d1e8a863138216292-1">1</div>
</div></td>
<td class="crayon-code">
<div class="crayon-pre">
<div id="crayon-59082d1e8a863138216292-1" class="crayon-line"><span class="crayon-sy">.</span><span class="crayon-o">/</span><span class="crayon-v">buildessential</span></div>
</div></td>
</tr>
</tbody>
</table>
</div>
</div>
</div>
