# Description

These are my Pine Script indicators free to use. If you find any of my code helpful for your projects, I'd appreciate a donation, and a reference to my work in your projects.

Bitcoin: `bc1q77mmp5uzehkuafkwjcrlah5260895jf0szplsz`

# Installation

* Open the Pine Editor on TradingView.
* Paste the script code in raw format into the editor.
* Click *Save* and/or *Add to Chart*.

The indicator is saved under *My Scripts* in the *Indicators & Strategies* menu.

## Hardcoded Moving Averages:

This is indicator adds 5 moving averages to your chart. These are hardcoded to a specific time interval. Using this indicator you can set your moving averages to be based on the daily. When you are charting the hourly, you can always keep an eye on the daily. This way you don't have to switch between time frames constantly.

There's also a menu, which allows you to switch between different variants of moving averages. Simple, exponential, volume, weighted, and Hull moving averages are availabe.

![](images/Hardcoded_Moving_Averages.png)

![](images/Hardcoded_Moving_Averages_Menu.png)

## RSI-VWMA

RSI on a volume-weighted moving average.

![](images/RSI-VWMA.png)

## Peak Reversal

This indicator is supposed to help traders identify potential market reversal points. Please note this is not a buy/sell indicator!

Peak Reversal is an indicator, which can be used as an early identification for a reversal, or a reversion to the mean. In mean reversion we try to find the bar where we can be certain a reversal is in play, as opposed to the proverbial knife catch. Peak Reversal helps by identifying the bar that actually reversed trend. As you can see it is often accurate, but as you can tell, one has to be careful applying this indicator to their trading, as the trend can just continue onwards.

Additionally Peak Reversal uses the same Keltner channels it uses to identify reversals also as breakouts. By default the breakout indication is off, because the chart gets messy otherwise. You can turn it on manually in the settings. When these triangles appear, you can interpret the bars as potentially starting a strong trend. What you don't want to see is a rejection X followed by a breakout triangle obviously.

Note that by default the coloring is very subdued. That's just a personal preference. You can adjust to yours in the settings.

![](images/Peak_Reversal.png)

## Stochastic

Stochastic with thicker lines when %K, and %D are above the user defined up and down thresholds. Otherwise it's the same script from TradingView. Personally I don't like the purple background fill, so it's 100% transparent. Adjust if you don't like it.

![](images/Stochastic.png)

## DMI

A slight variation on the standard TradingView DMI indicator. This one shows trend zones visually through color variation. Other than that doesn't have any other fancy features. I might add some like trend strength indication or something along those lines. Because this is seen as "not useful" by TradingView, it can't be published on TradingView. 

![](images/DMI.png)