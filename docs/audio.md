# Audio

Audio is an important part of the gaming experience. In this section we will cover an audio solution for the Raspberry Pi Zero W. This solution is based on the I2S capabilities of the Raspberry Pi Zero. You can check the Raspberry Pi PCM pins [here](https://pinout.xyz/pinout/pcm).

**IMPORTANT**, this solution is not currently working for the Raspberry Pi Zero W 2, I am looking for a solution since it works perfectly for the Raspberry Pi Zero W.

## MAX98357A

The MAX98357A is a small I2S amplifier that can be used to output audio from the Raspberry Pi Zero. Adafruit sells it for a few dollars and has an [amazing tutorial](https://learn.adafruit.com/adafruit-max98357-i2s-class-d-mono-amp/raspberry-pi-wiring) on how to use it (wiring and software configuration). The full documentation of Adafruit MAX98357A can be found [here](https://cdn-learn.adafruit.com/downloads/pdf/adafruit-max98357-i2s-class-d-mono-amp.pdf). You can check in the documentation the supported speakers.

If the `dtoverlay=max98357a` provided by Adafruit is not working for you, you can try the following configuration:

```bash
dtoverlay=hifiberry-dac
dtoverlay=i2s-mmap
```

## Dealing with `VolumeControl::init() - Failed to find mixer elements` error

If you have RetroPie installed in your Raspberry Pi Zero, you might encounter the following error when booting games:

```bash
VolumeControl::init() - Failed to find mixer elements
```

This error will affect Raspberry Pi Zero W 2 users that tried to configure the audio and commented out the `dtparam=audio=on` line in the `/boot/config.txt` file. If you try to change audio configurations in the `raspi-config` tool, you will get the error that there is no audio device detected. This error does not affect gameplay, you can play without audio.

If you are going to play without audio, uncomment the `dtparam=audio=on` line in the `/boot/config.txt` file and select HDMI as the audio output in the `raspi-config` tool. This will likely fix the error and you will be able to play without audio. This [post in RetroPie](https://retropie.org.uk/forum/topic/26628/audio-issues-after-latest-raspbian-updates-june-2020) forum goes deeper into this issue.
