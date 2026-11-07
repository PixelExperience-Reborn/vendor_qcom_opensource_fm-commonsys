# PixelExperience-Reborn Android 17 — Qualcomm FM Commonsys / mondrian exclusion audit

**Review:** 2026-11-07. **Target:** independent AOSP17-based PE-Reborn on POCO F5 Pro / Redmi K60 (`mondrian`, SM8475). **Status:** historical source audited; optional FM stack **excluded from the product**. No Android17 build, FM HAL runtime test or RF capability test was performed.

## Decision

Preserve the historical Qualcomm FM source and its Git history for reference. **Do not add FM reception/transmission to the PE-Reborn mondrian device or the active Android17 manifest.** Do not build or install `FM2`, `qcom.fmradio`, `qcom.fmradio.xml`, `libqcomfm_jni`, FM HCI service/daemon, FM firmware, or a new FM VINTF/init/SELinux integration for this target.

The engineering choice is driven by product scope and the absence of a verified required FM consumer, **not by a claim that the SM8475 silicon can never support FM**. Android17 telephony/modem radio (RIL/IMS) and Wi-Fi/Bluetooth must remain available and are unrelated to the optional broadcast-FM application.

Modernize the PE-Reborn platform from reviewed, current Android17 AOSP first. Examine Qualcomm hardware-specific releases only where needed by mondrian and only after source, API/ABI, licence, device and build verification. Other Android-derived projects are comparison references, not the source owner for this independent base. There is **no reason to import the FM app or perform a speculative FM framework port** for a device product that expressly excludes it.

## Source evidence (exact commits)

| Reference | Commit and observations |
| --- | --- |
| [Historical PE FM Commonsys `seventeen`](https://github.com/PixelExperience-Reborn/vendor_qcom_opensource_fm-commonsys/tree/seventeen) | `58563166635e9926d81ef641602b2b314aefb008` (2023-10-14, QSSI14 merge). `fourteen` and `seventeen` resolve to this same commit; this is **not an Android17 port**. Tree contains 446 entries, including `fmapp2`, `jni`, `qcom/fmradio`, `helium`, `fm_hci`. |
| [LineageOS 24.0 FM Commonsys](https://github.com/LineageOS/android_vendor_qcom_opensource_fm-commonsys/tree/lineage-24.0) | `9ec99f94c26bff0bb9859985f445326a61d09ded`, 440 tree entries. It still builds a dedicated FM app and supporting JNI/Java FM modules; newer branch naming alone does not make this a required Android17 platform component. **Read-only comparison**. |
| [PE-Reborn AOSP17 staged manifest](https://github.com/PixelExperience-Reborn/manifest/tree/port/a17-aosp17-manifest-20261102) | Current manifest does not select `vendor/qcom/opensource/fm-commonsys` or `vendor/qcom/opensource/libfmjni`. Preserve this as a regression invariant. |
| [LF52406 mondrian device](https://github.com/LF52406/mondrian_android_device_xiaomi_mondrian/tree/lineage-24.0) and [sm8450-common](https://github.com/LF52406/mondrian_android_device_xiaomi_sm8450-common/tree/lineage-24.0) | Contemporary device/common hardware comparison only. A reference `common.mk` includes `libfmpal` in audio `PRODUCT_PACKAGES`; this line **is not proof of FM receiver capability nor a reason to include FM in the new PE product**. Independently audit any library dependencies before omitting or removing it in a future new native PE-Reborn device tree. |

## Build modules / incompatibility boundaries

- `fmapp2/Android.bp`: privileged, platform-certified `FM2` with `platform_apis`, FM Java library `qcom.fmradio`, JNI `libqcomfm_jni` and permissions XML. This is not a generic Android17 system API or required base service.
- `qcom/fmradio/Android.bp`: `qcom.fmradio` Java library and `qcom.fmradio.xml` permissions registration. Do not install either for mondrian.
- `jni/Android.bp`: shared `libqcomfm_jni` depends on Android runtime/nativehelper and FM-specific include path. No reason to change platform JNI/ART interfaces to accommodate it.
- `fm_hci/` and `helium/`: FM-specific native support. No verified active PE-Reborn AOSP17 product consumer, hence no FM daemon/HAL or vendor property/SELinux/VINTF additions.
- The distinct historical `vendor_qcom_opensource_libfmjni` repo must be excluded as well. Do not confuse a directory's presence in archived source with an installed module.
- A source-only manifest check **cannot** prove the absence of vendor prebuilt FM components or device `PRODUCT_PACKAGES`; this needs a later device-tree dependency check, Soong module graph and product-files inspection.

## Acceptance checks

1. PE-Reborn AOSP17 `default.xml`, all 25 source profiles and nondefault experimental source candidates must not select either FM repository by checkout path **or** archived repository name.
2. The independent `mondrian` and `sm8450-common` device trees, once authored, must not request `FM2`, `qcom.fmradio`, `qcom.fmradio.xml`, `libqcomfm_jni`, FM HCI/daemon, FM init/VINTF or FM-specific feature declarations.
3. Analyze the reference `libfmpal` audio package and any transitive vendor dependency on the actual PE product before choosing to omit the module. Do not delete a potentially shared dependency blindly.
4. Preserve Android17's official source owners, modern telephony/RIL/Bluetooth/Wi-Fi and the display SDM/DRM blocker. No FM-related source checkout must change these.
5. Run offline manifest regression tests, official `repo manifest`, Soong/module graph and installed-artifact scans when an independent PE-Reborn mondrian target exists. **No product build or RF test has been claimed.**

**Disposition:** source review complete; `vendor/qcom/opensource/fm-commonsys` **excluded from the PE-Reborn mondrian Android17 product**. Historical repository remains intact.
