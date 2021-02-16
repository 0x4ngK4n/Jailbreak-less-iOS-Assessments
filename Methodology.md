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
    - iPhone needs to be re-Jailbroken after every reboot cycle.
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

## The IPA Patching Process
- Use Objection to make Frida-Gadget injected IPA file
  - Use the flag: --skip-cleanup to get the Frida Gadget-injected IPA
    - Sample Command: ```objection patchipa --source <ipa file> --codesign-signature <signature> --skip-cleanup```
    - Use the below command to get the codesign value. This is required for the aforementioned objection patching command:
    
      <img width="1056" alt="image" src="https://user-images.githubusercontent.com/65641440/108062576-f69eb900-7094-11eb-82fa-1125e1d18d7d.png">
    - Take note of the Temporary folder from where codesigned IPA is copied by Objection.
      
      <img width="1056" alt="image" src="https://user-images.githubusercontent.com/65641440/108062993-8b091b80-7095-11eb-9e74-27f68e8c0cda.png">
    - Copy the Frida Gadget patched IPA from the temporary folder to working directory.
      
      <img width="964" alt="image" src="https://user-images.githubusercontent.com/65641440/108063299-f3f09380-7095-11eb-88c2-db4bd5bc4c29.png">
    - Use iOS App Signer to sign this Application.
      
      <img width="1063" alt="image" src="https://user-images.githubusercontent.com/65641440/108063384-1682ac80-7096-11eb-992f-0b510799964b.png">
      
## Deploying the Patched Application
- Unzip the patched IPA file which we obtained in the previous step.

  <img width="1032" alt="image" src="https://user-images.githubusercontent.com/65641440/108063710-9741a880-7096-11eb-993a-53acbeabfc9a.png">
- Use _ios-deploy_ to deploy the patched application to the iPhone.
    - Command: ```ios-deploy --bundle Payload/*.app --debug -W```
    
      <img width="1032" alt="image" src="https://user-images.githubusercontent.com/65641440/108063893-dd970780-7096-11eb-9983-4e8649740a88.png">
- When the deployment is successful, we should see the Frida Gadget listening on localhost, port : 27042
  <img width="1058" alt="image" src="https://user-images.githubusercontent.com/65641440/108064105-29e24780-7097-11eb-9f21-fd5a5b6e308f.png">
- At this point, we can start instrumenting the application using Frida or Objection
  <img width="1058" alt="image" src="https://user-images.githubusercontent.com/65641440/108064211-4e3e2400-7097-11eb-96ca-54f3b5d61e77.png">
  
## Confiuring the Frida Dylib
- By Default, Frida Gadget listens over localhost. However, we can change this behaviour by use of a Frida Gadget config file.

  <img width="1018" alt="image" src="https://user-images.githubusercontent.com/65641440/108064330-79c10e80-7097-11eb-859e-1cc4ee45c1a7.png">
  As you can see above, I have used the address "0.0.0.0" in config file to make Gadget listen on all interfaces.
- Now, when the IPA is deployed, the Gadget is listening on all interfaces(0.0.0.0)

  <img width="1044" alt="image" src="https://user-images.githubusercontent.com/65641440/108064644-f358fc80-7097-11eb-8980-9b05a762aa30.png">
- Now we can launch objection or Frida by referencing the IP Address of the iPhone.

  <img width="1044" alt="image" src="https://user-images.githubusercontent.com/65641440/108064745-12578e80-7098-11eb-8e25-4e15352a9a96.png">

## Code Signing Issue & Caveats
- Objection uses an in-built tool called 'node-applesign'. However, during the test this functionality was not working. 
    - Hence, iOS App Signer tool was used in this case.
- While using the iOS App Signer, uncheck the 'No get-task-allow'. This options prevents Frida from hooking to the patched application.

  <img width="986" alt="image" src="https://user-images.githubusercontent.com/65641440/108065147-87c35f00-7098-11eb-9f0b-255b275609fe.png">

## Few Tests
Now that we have a patched iOS Application (IPA file), we can run some commands over Objection/Frida to demonstrate few test cases.
- Listing classes inside the application:

  <img width="1013" alt="image" src="https://user-images.githubusercontent.com/65641440/108065321-cc4efa80-7098-11eb-871e-c083099c9caa.png">
- Listing methods inside relevant class

  <img width="1013" alt="image" src="https://user-images.githubusercontent.com/65641440/108065375-dec93400-7098-11eb-9305-9db6cdf6eec4.png">
  
## Sample Security Assessment Test Case - Touch ID Bypass
- The sample application in this context (DVIA-v2) validates fingerprint using evaluatePolicy in LAContext class.
  - In this case, Objection shall be used to hook into the LAContext class to always return true.
  - This allows us to trivially bypass the touchID check implemented by the sample application.
- To bypass touch ID of DVIA-v2, we can do so by  using Objection's inbuilt command: ```ios ui biometric bypass```
  
  <img width="1044" alt="image" src="https://user-images.githubusercontent.com/65641440/108065581-31a2eb80-7099-11eb-8f34-4a7fc964ccac.png">
  **Note**: Other applications may use more complex checks and we may need to write custom Frida.re scripts for bypassing such checks.
  
- Before Biometric Bypass:

  <img width="280" alt="image" src="https://user-images.githubusercontent.com/65641440/108065978-b2fa7e00-7099-11eb-8232-89dcb6969c86.png">
- After Biometric Bypass (**still wrong fingerprint but authetication is successful**):

  <img width="286" alt="image" src="https://user-images.githubusercontent.com/65641440/108066043-c9a0d500-7099-11eb-8067-c7266d0f989e.png">
  
## Conclusion
This is not even the tip of the iceberg. A multitude of in-depth security test cases are possible, thanks to the runtime magic of Frida.
iOS application assessment need not be only relied upon a Jailbroken device.
