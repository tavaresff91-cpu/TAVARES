name: Windows Cloud PC - Anydesk (Fast Optimized)

on:
  workflow_dispatch:

jobs:
  build:
    name: Start Cloud PC
    runs-on: windows-latest
    timeout-minutes: 4320  # 3 dias (tempo máximo sem travar pipeline)

    steps:
      - name: Download & Install Essentials
        run: |
          Invoke-WebRequest -Uri "https://www.dropbox.com/scl/fi/7eiczvgil84czu55dxep3/Downloads.bat?rlkey=wzdc1wxjsph2b7r0atplmdz3p&dl=1" -OutFile "Downloads.bat"
          cmd /c Downloads.bat

      - name: Start AnyDesk
        run: |
          if (Test-Path "start.bat") {
            cmd /c start.bat
          } else {
            Write-Host "start.bat não encontrado."
          }

      - name: Check AnyDesk Once (Fast)
        run: |
          $process = Get-Process -Name "AnyDesk" -ErrorAction SilentlyContinue
          if (-not $process) {
            Write-Host "AnyDesk não estava rodando, iniciando..."
            cmd /c start.bat
          } else {
            Write-Host "AnyDesk OK."
          }

      - name: Keep PC Alive
        run: |
          # Mantém a máquina viva mas sem loops pesados
          Start-Sleep -Seconds 10800   # 3 horas online

      - name: Cleanup
        if: always()
        run: |
          if (Test-Path "Downloads.bat") { Remove-Item Downloads.bat -Force }
          Write-Host "Limpeza concluída."
