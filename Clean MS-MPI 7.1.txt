@echo off
set LOGFILE=%TEMP%\blueCFD_uninstall_log.txt
echo Uninstall started at %DATE% %TIME% > "%LOGFILE%"

echo.
echo === Attempting to uninstall Microsoft MPI... ===
echo Attempting to uninstall Microsoft MPI... >> "%LOGFILE%"
msiexec /x {8499ACD3-C1E3-45AB-BF96-DA491727EBE1} /quiet /norestart
if %ERRORLEVEL% EQU 0 (
    echo Microsoft MPI uninstalled successfully. >> "%LOGFILE%"
) else (
    echo Failed to uninstall Microsoft MPI. Return code: %ERRORLEVEL% >> "%LOGFILE%"
)

echo.
echo === Deleting blueCFD folders... ===
set FOLDERS_TO_DELETE="C:\Program Files\blueCFD-Core-2017" "C:\Program Files\blueCFD-Core-2017-2" "C:\Program Files\blueCFD-Core-2020" "%USERPROFILE%\blueCFD-Core"

for %%F in (%FOLDERS_TO_DELETE%) do (
    if exist %%F (
        echo Deleting folder %%F >> "%LOGFILE%"
        rd /s /q %%F
    ) else (
        echo Folder %%F not found, skipping... >> "%LOGFILE%"
    )
)

echo.
echo === Cleaning up environment variables... ===
:: You can add more specific environment vars if needed
reg delete "HKCU\Environment" /v "blueCFD_ROOT" /f >> "%LOGFILE%" 2>&1
reg delete "HKCU\Environment" /v "WM_PROJECT_DIR" /f >> "%LOGFILE%" 2>&1

:: Clean PATH from blueCFD entries (simple removal – advanced cleaning requires parsing PATH)
setx PATH "%PATH:"=%" /M >> "%LOGFILE%" 2>&1

echo.
echo Uninstallation complete. Log saved to: %LOGFILE%
pause
