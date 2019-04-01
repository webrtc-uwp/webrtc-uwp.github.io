# Audio device enumeration and selection

While the WebRTC APIs in Chromium originally supported enumeration and selection of the audio capture (microphone) and render (speaker) devices, this API was removed in later releases.  This behavior is now handled in system-specific call for all native platforms, but is not part of the core WebRTC engine.  WebRTC on Windows will use the system default capture and render devices if they are not specified.

The following code from the `MainViewModel.cs` file of the `PeerCC` sample demonstrates enumeration & setting of capture and render devices:

``` C#
foreach (DeviceInformation audioInput in await DeviceInformation.FindAllAsync(MediaDevice.GetAudioCaptureSelector()))
{
    Conductor.MediaDevice audioInputDevice = new Conductor.MediaDevice();
    audioInputDevice.Id = audioInput.Id;
    audioInputDevice.Name = audioInput.Name;

    if (savedAudioRecordingDeviceId != null && savedAudioRecordingDeviceId == audioInput.Id)
    {
        SelectedMicrophone = audioInputDevice;
        // Note: This property calls Conductor.Instance.SelectAudioCaptureDevice(audioInputDevice);
    }
    Microphones.Add(audioInputDevice);
}

if (SelectedMicrophone == null && Microphones.Count > 0)
{
    SelectedMicrophone = Microphones.First();
}

foreach (var audioOutput in await DeviceInformation.FindAllAsync(MediaDevice.GetAudioRenderSelector()))
{
    Conductor.MediaDevice audioOutputDevice = new Conductor.MediaDevice();
    audioOutputDevice.Id = audioOutput.Id;
    audioOutputDevice.Name = audioOutput.Name;

    if (savedAudioPlayoutDeviceId != null && savedAudioPlayoutDeviceId == audioOutput.Id)
    {
        SelectedAudioPlayoutDevice = audioOutputDevice;
        // Note: This property calls Conductor.Instance.SelectAudioPlayoutDevice(audioOutputDevice);
    }
    AudioPlayoutDevices.Add(audioOutputDevice);
}

if (SelectedAudioPlayoutDevice == null && AudioPlayoutDevices.Count > 0)
{
    SelectedAudioPlayoutDevice = AudioPlayoutDevices.First();
}
```