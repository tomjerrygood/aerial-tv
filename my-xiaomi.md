# Aerial 电视桌面安装与配置记录 (Xiaomi TV)

- **日期**：2026-10-28
- **目标项目**：Aerial 电视桌面 (`https://github.com/tomjerrygood/aerial-tv` 已克隆至 `D:\github\aerial-tv`)

---

## 电视设备信息
1. **电视 1 (192.168.9.86 - Redmi Max 86)**：
   - 型号 (`ro.product.model`)：`MiTV-MFTP0`
   - 代号 (`ro.product.device`)：`alita`
2. **电视 2 (192.168.9.85 - Redmi S85)**：
   - 型号 (`ro.product.model`)：`MiTV-MTEQ0`
   - 代号 (`ro.product.device`)：`baymax`

---

## 工作要点与核心命令

### 1. ADB 连接与环境检查
```bash
adb connect <电视IP>:5555
adb devices
adb shell getprop ro.build.version.sdk
adb shell getprop ro.build.version.release
```

### 2. APK 下载与安装
```bash
curl.exe -fL -o aerial-standard.apk https://aerial.qust.me/download/aerial-standard.apk
adb install -r aerial-standard.apk
```

### 3. 权限授予与 AOT 编译优化
```bash
adb shell pm grant app.orchard.tv android.permission.ACCESS_FINE_LOCATION
adb shell pm grant app.orchard.tv android.permission.WRITE_SECURE_SETTINGS
adb shell appops set app.orchard.tv SYSTEM_ALERT_WINDOW allow
adb shell appops set app.orchard.tv WRITE_SETTINGS allow
adb shell appops set app.orchard.tv REQUEST_INSTALL_PACKAGES allow
adb shell appops set app.orchard.tv GET_USAGE_STATS allow
adb shell appops set app.orchard.tv android:get_usage_stats allow
adb shell cmd notification allow_listener app.orchard.tv/app.orchard.tv.guard.RevivalListener
adb shell am start -W -n app.orchard.tv/.MainActivity
adb shell cmd package compile -m speed -f app.orchard.tv
```

### 4. 保持 ADB 常开、设置默认桌面与无障碍守护
```bash
# 保持 ADB 常开
adb shell am broadcast -a app.orchard.tv.CONFIG -n app.orchard.tv/.guard.ConfigReceiver --ez keep_adb true

# 设置为默认桌面
adb shell cmd package set-home-activity --user 0 app.orchard.tv/.MainActivity

# 开启无障碍守护 (HomeGuardService)
adb shell settings put secure enabled_accessibility_services "app.orchard.tv/app.orchard.tv.guard.HomeGuardService"
adb shell settings put secure accessibility_enabled 1
```

---

## 验收输出记录

### 电视 1 (192.168.9.86 - Redmi Max 86 / MiTV-MFTP0) 验收输出：
```text
priority=0 preferredOrder=0 match=0x108000 specificIndex=-1 isDefault=true
app.orchard.tv/.MainActivity
app.orchard.tv/app.orchard.tv.guard.HomeGuardService
1
  mCurrentFocus=Window{baa5dc8 u0 app.orchard.tv/app.orchard.tv.MainActivity}
```

### 电视 2 (192.168.9.85 - Redmi S85 / MiTV-MTEQ0) 验收输出：
```text
priority=0 preferredOrder=0 match=0x108000 specificIndex=-1 isDefault=true
app.orchard.tv/app.orchard.tv.guard.HomeGuardService
  mCurrentFocus=Window{a61b11 u0 app.orchard.tv/app.orchard.tv.MainActivity}
```
