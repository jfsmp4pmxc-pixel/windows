name: 🚀 SEVER AI STV PREMIUM

on:
  workflow_dispatch:
    inputs:
      duration:
        description: '🕐 Thời gian sử dụng'
        required: false
        default: '5h40m'
        type: choice
        options:
        - '1h'
        - '3h' 
        - '5h40m'

jobs:
  Premium-RDP-Setup:
    runs-on: windows-latest
    timeout-minutes: 340
    
    steps:
      # BANNER CHÀO MỪNG
      - name: 🎯 KHỞI ĐỘNG HỆ THỐNG
        run: |
          Write-Host ""
          Write-Host ""
          Write-Host "🤖 AI STV PREMIUM RDP SERVER" -ForegroundColor Yellow
          Write-Host "⚡ Powered by AI STV" -ForegroundColor Green
          Write-Host "🕒 Thời gian: ${{ github.event.inputs.duration }}" -ForegroundColor Magenta
          Write-Host ""
          
          # Hiệu ứng loading
          $frames = @('⣾', '⣽', '⣻', '⢿', '⡿', '⣟', '⣯', '⣷')
          for($i=0; $i -lt 10; $i++) {
              Write-Host "`r🚀 Đang khởi tạo hệ thống... $($frames[$i % 8])" -NoNewline -ForegroundColor Blue
              Start-Sleep -Milliseconds 100
          }
          Write-Host "`r✅ Hệ thống đã sẵn sàng!                    " -ForegroundColor Green

      # CẤU HÌNH NÂNG CAO
      - name: 🔧 CẤU HÌNH HỆ THỐNG
        run: |
          Write-Host ""
          Write-Host "🛠️  ĐANG CẤU HÌNH HỆ THỐNG" -ForegroundColor Yellow
          
          $steps = @(
              @{Name="Bật Remote Desktop"; Script={ 
                  Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 0 -Force
              }},
              @{Name="Tắt xác thực mạng"; Script={
                  Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "UserAuthentication" -Value 0 -Force
              }},
              @{Name="Cấu hình bảo mật RDP"; Script={
                  Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "SecurityLayer" -Value 0 -Force
                  Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server\WinStations\RDP-Tcp' -Name "UserAuthentication" -Value 0 -Force
              }},
              @{Name="Mở port 3389 firewall"; Script={
                  netsh advfirewall firewall add rule name="RDP-Premium" dir=in action=allow protocol=TCP localport=3389 profile=any
                  netsh advfirewall firewall add rule name="RDP-Premium-Out" dir=out action=allow protocol=TCP localport=3389 profile=any
              }},
              @{Name="Khởi động dịch vụ"; Script={
                  Set-Service -Name TermService -StartupType Automatic -ErrorAction SilentlyContinue
                  Set-Service -Name UmRdpService -StartupType Automatic -ErrorAction SilentlyContinue
                  Start-Service -Name TermService -ErrorAction SilentlyContinue
                  Start-Service -Name UmRdpService -ErrorAction SilentlyContinue
              }}
          )
          
          foreach($step in $steps) {
              Write-Host "│ 📋 $($step.Name)..." -NoNewline -ForegroundColor White
              try {
                  & $step.Script
                  Write-Host "`r│ ✅ $($step.Name)" -ForegroundColor Green
              } catch {
                  Write-Host "`r│ ⚠️  $($step.Name) - Đã bỏ qua lỗi nhỏ" -ForegroundColor Yellow
              }
              Start-Sleep -Milliseconds 500
          }
          
          Write-Host "🎯 Cấu hình hoàn tất!" -ForegroundColor Green

      # TẠO USER PREMIUM - (tối ưu mật khẩu)
      - name: 👤 TẠO TÀI KHOẢN PREMIUM
        run: |
          Write-Host ""
          Write-Host "👤 ĐANG TẠO TÀI KHOẢN PREMIUM" -ForegroundColor Yellow
          
          # Hàm tạo password ngắn (8 ký tự) đảm bảo có ít nhất 1 chữ hoa, 1 chữ thường, 1 số
          function New-PremiumPassword {
              param()
              $upper = 'ABCDEFGHJKLMNPQRSTUVWXYZ'   # loại I,O để tránh nhầm
              $lower = 'abcdefghijkmnpqrstuvwxyz'
              $numbers = '23456789'               # loại 0,1 để tránh nhầm
              $all = $upper + $lower + $numbers

              # đảm bảo 1 chữ hoa, 1 chữ thường, 1 chữ số
              $pw = ''
              $pw += $upper[(Get-Random -Minimum 0 -Maximum $upper.Length)]
              $pw += $lower[(Get-Random -Minimum 0 -Maximum $lower.Length)]
              $pw += $numbers[(Get-Random -Minimum 0 -Maximum $numbers.Length)]

              # thêm ký tự ngẫu nhiên để đủ 8 ký tự
              for ($i = 1; $i -le 5; $i++) {
                  $pw += $all[(Get-Random -Minimum 0 -Maximum $all.Length)]
              }

              # trộn ngẫu nhiên vị trí các ký tự rồi trả về
              return -join ($pw.ToCharArray() | Sort-Object { Get-Random })
          }

          # Nếu người dùng đặt mật khẩu tùy chỉnh (ví dụ bằng secret) thì ưu tiên dùng
          if ($env:CUSTOM_RDP_PASS) {
              $matKhau = $env:CUSTOM_RDP_PASS
              Write-Host "│ ℹ️  Dùng mật khẩu tùy chỉnh từ env" -ForegroundColor Blue
          } else {
              $matKhau = New-PremiumPassword
          }

          $securePass = ConvertTo-SecureString $matKhau -AsPlainText -Force
          
          # Xóa user cũ nếu tồn tại
          try {
              Remove-LocalUser -Name "AISTV-PREMIUM" -ErrorAction SilentlyContinue
              Write-Host "│ ✅ Đã xóa user cũ" -ForegroundColor Green
          } catch {
              Write-Host "│ ℹ️  Không có user cũ để xóa" -ForegroundColor Blue
          }
          
          # Tạo user mới với đầy đủ quyền
          try {
              New-LocalUser -Name "AISTV-PREMIUM" -Password $securePass -AccountNeverExpires -Description "AI STV Premium Account"
              Write-Host "│ ✅ Đã tạo user mới" -ForegroundColor Green
              
              # Đặt password không bao giờ hết hạn (dùng Set-LocalUser nếu cần)
              try {
                  Set-LocalUser -Name "AISTV-PREMIUM" -PasswordNeverExpires $true -ErrorAction SilentlyContinue
              } catch {
                  # Nếu môi trường không hỗ trợ Set-LocalUser, bỏ qua an toàn
              }

              # Thêm vào các nhóm cần thiết
              Add-LocalGroupMember -Group "Administrators" -Member "AISTV-PREMIUM" -ErrorAction SilentlyContinue
              Add-LocalGroupMember -Group "Remote Desktop Users" -Member "AISTV-PREMIUM" -ErrorAction SilentlyContinue
              Add-LocalGroupMember -Group "Users" -Member "AISTV-PREMIUM" -ErrorAction SilentlyContinue
              
              # Kích hoạt user
              Enable-LocalUser -Name "AISTV-PREMIUM"
              
              Write-Host "│ ✅ Đã cấp quyền Administrator & RDP" -ForegroundColor Green
              
          } catch {
              Write-Host "│ ❌ Lỗi tạo user: $($_.Exception.Message)" -ForegroundColor Red
              exit 1
          }
          
          # Lưu thông tin (cẩn trọng nếu repo public)
          echo "RDP_USER=AISTV-PREMIUM" >> $env:GITHUB_ENV
          echo "RDP_PASS=$matKhau" >> $env:GITHUB_ENV
          echo "$matKhau" > $env:TEMP\rdp_password.txt
          
          Write-Host "✅ Tài khoản: AISTV-PREMIUM đã sẵn sàng" -ForegroundColor Green

      # KIỂM TRA QUYỀN ĐĂNG NHẬP
      - name: 🔍 KIỂM TRA QUYỀN HỆ THỐNG
        run: |
          Write-Host ""
          Write-Host "🔍 KIỂM TRA QUYỀN ĐĂNG NHẬP" -ForegroundColor Yellow
          
          # Kiểm tra user tồn tại
          $user = Get-LocalUser -Name "AISTV-PREMIUM" -ErrorAction SilentlyContinue
          if ($user) {
              Write-Host "│ ✅ User AISTV-PREMIUM tồn tại" -ForegroundColor Green
              Write-Host "│ 📊 Trạng thái: $($user.Enabled)" -ForegroundColor White
          } else {
              Write-Host "│ ❌ User không tồn tại" -ForegroundColor Red
              exit 1
          }
          
          # Kiểm tra quyền Administrator
          $isAdmin = (Get-LocalGroupMember -Group "Administrators" | Where-Object {$_.Name -like "*AISTV-PREMIUM"}).Count -gt 0
          if ($isAdmin) {
              Write-Host "│ ✅ Có quyền Administrator" -ForegroundColor Green
          } else {
              Write-Host "│ ❌ Không có quyền Administrator" -ForegroundColor Red
          }
          
          # Kiểm tra quyền Remote Desktop
          $isRDPUser = (Get-LocalGroupMember -Group "Remote Desktop Users" | Where-Object {$_.Name -like "*AISTV-PREMIUM"}).Count -gt 0
          if ($isRDPUser) {
              Write-Host "│ ✅ Có quyền Remote Desktop" -ForegroundColor Green
          } else {
              Write-Host "│ ❌ Không có quyền Remote Desktop" -ForegroundColor Red
          }
          
          # Kiểm tra dịch vụ RDP
          $termService = Get-Service -Name TermService -ErrorAction SilentlyContinue
          if ($termService.Status -eq 'Running') {
              Write-Host "│ ✅ Dịch vụ TermService đang chạy" -ForegroundColor Green
          } else {
              Write-Host "│ ❌ Dịch vụ TermService không chạy" -ForegroundColor Red
          }
          
          Write-Host "🎯 Kiểm tra hoàn tất!" -ForegroundColor Green

      # TAILSCALE PREMIUM
      - name: 🌐 THIẾT LẬP MẠNG PREMIUM
        env:
          TAILSCALE_AUTH_KEY: ${{ secrets.TAILSCALE_AUTH_KEY }}
        run: |
          Write-Host ""
          Write-Host "🌐 ĐANG THIẾT LẬP KẾT NỐI MẠNG" -ForegroundColor Yellow
          
          # Cài đặt Tailscale
          try {
              $msiPath = "$env:TEMP\tailscale-premium.msi"
              Invoke-WebRequest -Uri "https://pkgs.tailscale.com/stable/tailscale-setup-latest-amd64.msi" -OutFile $msiPath
              Start-Process msiexec.exe -ArgumentList "/i", "`"$msiPath`"", "/quiet", "/norestart" -Wait
              Remove-Item $msiPath -Force -ErrorAction SilentlyContinue
              Write-Host "│ ✅ Cài đặt Tailscale thành công" -ForegroundColor Green
          } catch {
              Write-Host "│ ❌ Lỗi cài đặt Tailscale" -ForegroundColor Red
          }
          
          # Chờ dịch vụ khởi động
          Start-Sleep -Seconds 10
          
          # Kết nối Tailscale
          try {
              & "$env:ProgramFiles\Tailscale\tailscale.exe" up --authkey=$env:TAILSCALE_AUTH_KEY --hostname=ai-stv-premium-$env:GITHUB_RUN_ID --accept-routes --accept-dns --reset
              Write-Host "│ ✅ Kết nối Tailscale thành công" -ForegroundColor Green
          } catch {
              Write-Host "│ ❌ Lỗi kết nối Tailscale" -ForegroundColor Red
          }
          
          # Lấy IP với retry mechanism
          Write-Host "🔄 Đang lấy địa chỉ IP..." -NoNewline -ForegroundColor Blue
          $ip = $null
          for($i=0; $i -lt 20; $i++) {
              try {
                  $ip = & "$env:ProgramFiles\Tailscale\tailscale.exe" ip -4 2>&1
                  if ($ip -and $ip -match '^\d+\.\d+\.\d+\.\d+$') {
                      echo "TAILSCALE_IP=$ip" >> $env:GITHUB_ENV
                      Write-Host "`r✅ Địa chỉ IP: $ip" -ForegroundColor Green
                      break
                  }
              } catch {
                  # Bỏ qua lỗi và thử lại
              }
              Write-Host "`r🔄 Đang lấy địa chỉ IP... $(@('.', '..', '...')[$i % 3])" -NoNewline -ForegroundColor Blue
              Start-Sleep -Seconds 3
          }
          
          if (-not $ip -or $ip -notmatch '^\d+\.\d+\.\d+\.\d+$') {
              Write-Host "`r⚠️  Không thể lấy IP, sử dụng localhost" -ForegroundColor Yellow
              echo "TAILSCALE_IP=localhost" >> $env:GITHUB_ENV
          }

      # HIỂN THỊ GIAO DIỆN ĐẸP
      - name: 🎉 THÔNG TIN KẾT NỐI
        run: |
          $matKhau = Get-Content $env:TEMP\rdp_password.txt -ErrorAction SilentlyContinue
          if (-not $matKhau) {
              $matKhau = "Không thể đọc mật khẩu"
          }
          
          # Chuyển đổi thời gian từ input sang phút
          $durationInput = "${{ github.event.inputs.duration }}"
          switch ($durationInput) {
              "1h" { $totalMinutes = 60; $displayTime = "1 giờ" }
              "3h" { $totalMinutes = 180; $displayTime = "3 giờ" }
              "5h40m" { $totalMinutes = 340; $displayTime = "5 giờ 40 phút" }
          }
          
          # Tính thời gian chính xác
          $timeZone = [System.TimeZoneInfo]::FindSystemTimeZoneById("SE Asia Standard Time")
          $thoiGianBatDau = [System.TimeZoneInfo]::ConvertTimeFromUtc((Get-Date).ToUniversalTime(), $timeZone)
          $thoiGianKetThuc = $thoiGianBatDau.AddMinutes($totalMinutes)
          
          Write-Host ""
          Write-Host "┌───────────────────────────────────────────┐" -ForegroundColor Cyan
          Write-Host "│              🎉 KẾT NỐI THÀNH CÔNG!              │" -ForegroundColor Cyan
          Write-Host "├───────────────────────────────────────────┤" -ForegroundColor Cyan
          Write-Host "│ 🌐  Địa chỉ: $env:TAILSCALE_IP" -ForegroundColor White
          Write-Host "│ 👤  Tài khoản: AISTV-PREMIUM" -ForegroundColor White
          Write-Host "│ 🔐  Mật khẩu: $matKhau" -ForegroundColor White
          Write-Host "│ ⏰  Thời lượng: $displayTime" -ForegroundColor White
          Write-Host "│ 🕐  Bắt đầu: $($thoiGianBatDau.ToString('HH:mm:ss'))" -ForegroundColor White
          Write-Host "│ 🕐  Kết thúc: $($thoiGianKetThuc.ToString('HH:mm:ss'))" -ForegroundColor White
          Write-Host "│ 📍  Port: 3389" -ForegroundColor White
          Write-Host "├───────────────────────────────────────────┤" -ForegroundColor Cyan
          Write-Host "│ 💡  Hướng dẫn:                                    │" -ForegroundColor Cyan
          Write-Host "│   1. Mở Remote Desktop Connection                 │" -ForegroundColor Yellow
          Write-Host "│   2. Nhập: $env:TAILSCALE_IP                      │" -ForegroundColor Yellow
          Write-Host "│   3. User: AISTV-PREMIUM | Password: trên          │" -ForegroundColor Yellow
          Write-Host "│   4. Enjoy! 🚀                                    │" -ForegroundColor Yellow
          Write-Host "└───────────────────────────────────────────┘" -ForegroundColor Cyan
          
          # Lưu thời gian vào biến môi trường
          echo "TOTAL_MINUTES=$totalMinutes" >> $env:GITHUB_ENV

      # DUY TRÌ PHIÊN ỔN ĐỊNH
      - name: ⏳ DUY TRÌ PHIÊN LÀM VIỆC
        run: |
          $matKhau = Get-Content $env:TEMP\rdp_password.txt -ErrorAction SilentlyContinue
          $totalMinutes = $env:TOTAL_MINUTES
          
          # Tính thời gian chính xác
          $timeZone = [System.TimeZoneInfo]::FindSystemTimeZoneById("SE Asia Standard Time")
          $thoiGianBatDau = [System.TimeZoneInfo]::ConvertTimeFromUtc((Get-Date).ToUniversalTime(), $timeZone)
          $thoiGianKetThuc = $thoiGianBatDau.AddMinutes($totalMinutes)
          
          Write-Host ""
          Write-Host "🔄 BẮT ĐẦU DUY TRÌ PHIÊN LÀM VIỆC..." -ForegroundColor Yellow
          Write-Host "💎 Phiên: AI STV PREMIUM" -ForegroundColor Magenta
          Write-Host "🔗 Kết nối: $env:TAILSCALE_IP" -ForegroundColor Cyan
          Write-Host "🕐 Bắt đầu: $($thoiGianBatDau.ToString('HH:mm:ss'))" -ForegroundColor Green
          Write-Host "🕐 Kết thúc: $($thoiGianKetThuc.ToString('HH:mm:ss'))" -ForegroundColor Green
          Write-Host ""
          
          # Tạo process giám sát hệ thống
          $monitorScript = {
              $lastLogTime = Get-Date
              while ($true) {
                  $currentTime = Get-Date
                  
                  # Log trạng thái mỗi 2 phút
                  if (($currentTime - $lastLogTime).TotalMinutes -ge 2) {
                      # Kiểm tra dịch vụ
                      $termService = Get-Service -Name TermService -ErrorAction SilentlyContinue
                      $rdpConnections = Get-NetTCPConnection -LocalPort 3389 -ErrorAction SilentlyContinue | Where-Object {$_.State -eq 'Established'}
                      
                      Write-Host "[$($currentTime.ToString('HH:mm:ss'))] 📊 Trạng thái - RDP: $($termService.Status), Kết nối: $($rdpConnections.Count)" -ForegroundColor Blue
                      $lastLogTime = $currentTime
                  }
                  
                  # Kiểm tra và khởi động lại dịch vụ nếu cần
                  $termService = Get-Service -Name TermService -ErrorAction SilentlyContinue
                  if ($termService.Status -ne 'Running') {
                      Write-Host "[$($currentTime.ToString('HH:mm:ss'))] 🔄 Khởi động lại TermService..." -ForegroundColor Yellow
                      Start-Service -Name TermService -ErrorAction SilentlyContinue
                  }
                  
                  Start-Sleep -Seconds 30
              }
          }
          
          # Chạy monitor trong background
          Start-Job -ScriptBlock $monitorScript -Name "SystemMonitor"
          
          # Hiển thị timer chính
          $frames = @('⠋', '⠙', '⠹', '⠸', '⠼', '⠴', '⠦', '⠧', '⠇', '⠏')
          $colors = @('Red', 'Yellow', 'Green', 'Cyan', 'Blue', 'Magenta')
          $startTime = Get-Date
          
          do {
              $thoiGianHienTai = [System.TimeZoneInfo]::ConvertTimeFromUtc((Get-Date).ToUniversalTime(), $timeZone)
              $thoiGianConLai = $thoiGianKetThuc - $thoiGianHienTai
              
              $gioConLai = [math]::Max(0, [math]::Floor($thoiGianConLai.TotalHours))
              $phutConLai = [math]::Max(0, [math]::Floor($thoiGianConLai.TotalMinutes % 60))
              $giayConLai = [math]::Max(0, [math]::Floor($thoiGianConLai.TotalSeconds % 60))
              
              $frame = $frames[([int]((Get-Date) - $startTime).TotalSeconds) % $frames.Length]
              $color = $colors[([int]((Get-Date) - $startTime).TotalMinutes) % $colors.Length]
              
              Write-Host "`r$frame [$($thoiGianHienTai.ToString('HH:mm:ss'))] Thời gian còn lại: $gioConLai giờ $phutConLai phút $giayConLai giây" -NoNewline -ForegroundColor $color
              
              Start-Sleep -Seconds 5
              
          } while ($thoiGianConLai.TotalSeconds -gt 0)
          
          Write-Host ""
          Write-Host "🎯 ĐÃ HẾT THỜI GIAN - TỰ ĐỘNG DỪNG!" -ForegroundColor Red
          
          # Dọn dẹp jobs
          Get-Job -Name "SystemMonitor" -ErrorAction SilentlyContinue | Stop-Job -PassThru | Remove-Job -Force

      # DỌN DẸP CHUYÊN NGHIỆP
      - name: 🧹 DỌN DẸP HỆ THỐNG
        if: always()
        run: |
          Write-Host ""
          Write-Host "🧹 ĐANG DỌN DẸP HỆ THỐNG..." -ForegroundColor Yellow
          
          $cleanupSteps = @(
              @{Name="Xóa file password"; Script={
                  Remove-Item "$env:TEMP\rdp_password.txt" -ErrorAction SilentlyContinue
              }},
              @{Name="Vô hiệu hóa user"; Script={
                  Disable-LocalUser -Name "AISTV-PREMIUM" -ErrorAction SilentlyContinue
              }},
              @{Name="Đóng kết nối mạng"; Script={
                  & "$env:ProgramFiles\Tailscale\tailscale.exe" down --accept-risk=all -ErrorAction SilentlyContinue
                  Start-Sleep -Seconds 5
              }},
              @{Name="Xóa rule firewall"; Script={
                  netsh advfirewall firewall delete rule name="RDP-Premium" dir=in -ErrorAction SilentlyContinue
                  netsh advfirewall firewall delete rule name="RDP-Premium-Out" dir=out -ErrorAction SilentlyContinue
                  Remove-NetFirewallRule -DisplayName "RDP-Premium" -ErrorAction SilentlyContinue
                  Remove-NetFirewallRule -DisplayName "RDP-Premium-Out" -ErrorAction SilentlyContinue
              }},
              @{Name="Khôi phục cài đặt RDP"; Script={
                  Set-ItemProperty -Path 'HKLM:\System\CurrentControlSet\Control\Terminal Server' -Name "fDenyTSConnections" -Value 1 -Force -ErrorAction SilentlyContinue
                  Stop-Service -Name TermService -Force -ErrorAction SilentlyContinue
              }}
          )
          
          foreach($step in $cleanupSteps) {
              Write-Host "│ 🗑️  $($step.Name)..." -NoNewline -ForegroundColor Gray
              try {
                  & $step.Script
                  Write-Host "`r│ ✅ $($step.Name)" -ForegroundColor Green
              } catch {
                  Write-Host "`r│ ⚠️  $($step.Name) - Đã xử lý an toàn" -ForegroundColor Yellow
              }
              Start-Sleep -Milliseconds 300
          }
          
          # Dọn dẹp jobs còn sót
          Get-Job | Stop-Job -PassThru | Remove-Job -Force -ErrorAction SilentlyContinue
          
          Write-Host ""
          Write-Host "🎉 Dọn dẹp hoàn tất! Hẹn gặp lại! 👋" -ForegroundColor Green
