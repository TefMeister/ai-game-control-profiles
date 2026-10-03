# RE2 (2019): launch with the game folder as the working directory (hazard)

Profile: `profiles/resident-evil-2-2019.json` -> hazards. Confidence: verified-live 2026-10-03, n=6 launches.

RE2 reads and writes `re2_config.ini` in its WORKING DIRECTORY, not beside `re2.exe`. Started from another folder
(for example `start re2.exe` from a shell sitting in some repo), it creates a fresh config there without
`TargetPlatform=DirectX12` and stays black before the title; with REFramework VR the log repeats
`VR: Failed to get primary camera!` every frame, and title scripts never see the title. Fix: start it with the game
folder as the working directory (PowerShell `Start-Process -WorkingDirectory`, or `visceral-re2-vr/dev-archive/tools/
re2drive.py launch`). Also: a "closed" game can linger a few seconds; a launch while it still runs does nothing, so wait
for the process to exit before launching again.
