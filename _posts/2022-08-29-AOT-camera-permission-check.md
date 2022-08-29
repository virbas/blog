---
layout: single
title: "Hololens AOT camera and microphone permission check"
date: 2022-08-29
comments: true
---

Alright, this is a short one. Ever felt annoyed about the camera/microphone permissions popup showing up when you start your camera, but instead you would like to have it shown when the app starts?

Here's how to do it:

```csharp
private async System.Threading.Tasks.Task CheckAppPermissions()
{
#if WINDOWS_UWP && !UNITY_EDITOR
  var mediaCapture =  new Windows.Media.Capture.MediaCapture();
  try
  {
    await mediaCapture.InitializeAsync(new Windows.Media.Capture.MediaCaptureInitializationSettings
    {
        StreamingCaptureMode = Windows.Media.Capture.StreamingCaptureMode.AudioAndVideo
    });
  }
  catch(System.Exception ex)
  {
    /**
    * Camera & mic permissions were rejected just now or any time before, even on the previous app run.
    * As far as I understand, there is no way to trigger the permission dialog again and the only choice we * leave the user with - manually enable permissions in the settings on reinstall the app...
    * Note - code below written using MRTK2, adapt if necessary..
    */
    var hiddenWindows = this.HideAllWindows();
    var dialog = Microsoft.MixedReality.Toolkit.UI.Dialog.Open(permissionsDialog, DialogButtonType.Close, "App requires camera and microphone permissions.", "Allow the permissions in the system settings or reinstall the app.", true);
    dialog.OnClosed += (Microsoft.MixedReality.Toolkit.UI.DialogResult obj) =>
    {
      // For example: Application.Quit();
    };
  }
  mediaCapture.Dispose();
#endif
}
```
