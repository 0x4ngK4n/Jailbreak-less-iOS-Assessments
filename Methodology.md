# 📱Jailbreak-less iOS Application Security Assessment

## In the following sections, I address the following main points:
1. Why there is a need for Jailbreak-less Assessments
2. What are the obsctacles to this approach?
3. The tools required to set-up the environment
4. The Patching Process via Objection
5. Frida Gadget File
6. Sample Test Cases

## Why no Jailbreak iOS Application Assessment?
- With every new iteration of iOS, Jailbreaks are becoming increasigly complex.
- _Usually_ a consumer uses a non-Jailbroken iPhone.
  - Consumers are more aware of the risks of running critical applications on Jailbroken phones.
  - Jailbreaks are usually un-tethered increasing complexity of phone usage for a normal user.
    - Phone needs to be re-Jailbroken after every reboot cycle.
- From an iOS Developers' viewpoint, including Jailbreak detection checks in applications have become a norm.
- Overall, a Jailbreak-less assessment presents a more realistic view for Mobile Security Aplication Assessment.

## Methodology
A combination of Static and Dynamic Analysis can be applied to conduct a Jailbreak-less Application Security Assessment.
- Static Analysis:
  - Tools such as IDA Pro and Hopper may be employed.
    - Likely Use Cases: To bypass checks such as anti-tampering and/ or Anti-debugging.
    - This is to prepare the application for next-phase: Dynamic/ runtime analysis.
- Dynamic Analysis:
  - Frida (https://frida.re)
  - Objection (https://github.com/sensepost/objection)
  
## Set-up
The following tools are required as pre-requisite before starting such an assessment:
- A Macbook or a MacOS running on virtualization software
- Xcode: To generate the mobile provisioning file, which is later used by Objection while generating the patched IPA
- Access to Apple Developer Account
  - Ease of deployability of custom/patched IPA to iPhone
- A Non-jailbroken iPhone for deploying Patched IPA file

## Why need a Provisioning File?
- What is a provisioning Profile?
  - A collection of digital entities that uniquely ties developers and devices to an authorised iPhone Development Team and enables a device to be used for testing.
- What does a provisioning profile contain?
  - Application ID: A string used to identify apps from a development team.
  - The development certificate
  - The Device ID: The devices on which the testing application can be run.
- Why we need a provisioning Profile?
  - Unlike Android phone, we can't install any application on an iOS device.
  - The testing device has to be linked to a developer account.
  - On an iDevice, the application (signed binary) is validated against the provisioning profile.
  
 Reference: https://abhimuralidharan.medium.com/what-is-a-provisioning-profile-in-ios-77987a7c54c2

## Generating a Provisioning File
- We can generate a provisioning profile by creating an empty Xcode project.
- Launch this project on a physical test device (the Non-Jailbroken iPhone) such that 'embedded.mobileprovision' file is generated on the computer.
- This provisioning will later be used by Objection to generate the patched IPA.

## The Patching Process Tools
- Objection to do automated dylib injection
  - Installation: pip3 install objection
- Frida to conduct runtime analysis of the application
  - Installation: pip3 install frida-tools
- App signing via iOS App Signer to code-sign patched IPA
  - More information: https://dantheman827.github.io/ios-app-signer/
- iOS-deploy for installing patched IPA on iPhone
  - More information: https://github.com/ios-control/ios-deploy

## Potential Obstacles to Patching Process
- An assessor may need to bypass following checks:
  - Anti-debugging Mechanisms
  - Anti-tampering Mechanisms
- Static Analysis & reverse-engineering plays a key role in this case:
  - Assessor may need to reverse the Application to bypass such protections
  - Effort can be more time-consuming if Application has code obfuscation

## 

  

