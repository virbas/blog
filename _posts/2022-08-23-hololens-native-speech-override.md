---
layout: single
title: "Overwriting Hololens Speech Commands."
date: 2022-08-23
comments: true
---

This article is only intended for those who get frustrated by the hololens system commands getting in your way.
Sources of  frustration:
- You want to implement your own routines for capturing a photo or video, instead of relying on the built-onHololens version, which is triggered by the verbal command, “Take a picture,” or “Take a video.” Using the phrase,“Take a picture” should execute your code and bypass the Hololens default implementation.
- You are in a virtual collaboration session and want to instruct the remote peer to “Restart the device”. You can guess what will get triggered on your hololens device…
- You want to see the speech commands supported by your app instead of using the global Hololens options, which can be initiated by saying, “what can I say.”

Thankfully there is a method for doing exactly that. But beware - you will lose all of the default Hololens commands, and may  need to recreate them from scratch. I’ll leave this for you to decide if it’s worth the effort.

## Tools
For this demo we’ll be using **MRTK** and Unity to build for Hololens. The code applies to both **MRTK2** and the brand new **MRTK3**.

## Solution..
Quite simple! Fire up the `Windows.Media.SpeechRecognition.SpeechRecognizer` and it will magically block all the speech commands from triggering the Hololens global speech recognition subsystem.
```csharp
#if WINDOWS_UWP && !UNITY_EDITOR
  private static SpeechRecognizer speechRecognizer = null;
  async void StartSpeechRecognizer()
  {
    speechRecognizer = new SpeechRecognizer();
    speechRecognizer.Timeouts.InitialSilenceTimeout = TimeSpan.FromSeconds(4);
    await speechRecognizer.CompileConstraintsAsync();
    speechRecognizer.ContinuousRecognitionSession.Completed += OnDictationComplete;
    await speechRecognizer.ContinuousRecognitionSession.StartAsync();
  }

  async private void OnDictationComplete(SpeechContinuousRecognitionSession session, SpeechContinuousRecognitionCompletedEventArgs args)
  {
    if (speechRecognizer != null)
    {
      try {
        await speechRecognizer.ContinuousRecognitionSession.StartAsync();
      } catch(Exception ex) {
         Debug.Log($"Failed restarting continuous session: {ex.Message}");           
      }
    }
  }
  
  async private void Cleanup()
  {
    speechRecognizer.ContinuousRecognitionSession.Completed += OnDictationComplete;
    await speechRecognizer.ContinuousRecognitionSession.StopAsync();
    speechRecognizer.Dispose();
    speechRecognizer = null;
  };
#endif
```

One thing to mention, `SpeechRecognizer` will stop listening after either `InitialSilenceTimeout` or `EndSilenceTimeout` has passed and raise the Completed event. Make sure you handle it and restart the listening session.
And, of course, don’t forget to clean up when you no longer need the recognizer (including `Application.quitting` event).


## Extras
If you wish - you can handle the full phrase recognition events directly using the `SpeechRecognizer` including hypothesis generation or grammar constraints. Keep in mind that `Windows.Media.SpeechRecognition` is only available for UWP builds. I recommend implementing a very similar routine using `UnityEngine.Windows.Speech` as you’re progressing with your app development in the editor.

If you no longer need to rely on **MRTK’s** speech command subsystem, you should disable it the **MRTK** settings:

*Set “Start Behavior” to “Manual Start” for MRTK 2.x*
![alt]({{ site.url }}{{ site.baseurl }}/assets/images/2022-08-23-overwriting-hololens-speech-commands/mrtk-disable-speech.png)
{: .full}

-----
*Disable PhraseRecognitionSubsystem for MRTK 3.x*
![alt]({{ site.url }}{{ site.baseurl }}/assets/images/2022-08-23-overwriting-hololens-speech-commands/mrtk3-disable-speech.png)
{: .full}



