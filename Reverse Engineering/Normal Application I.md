# Normal Application I

Can you reverse the application and give me the flag

Author: R1ckyH

Flag Format: PUCTF25{[a-zA-Z0-9_]+}

---

There are two methods discussed in this write-up: Method 1 : and Method 2 :. Method 1 was our solution during the competition, while Method 2 is based on the hints provided by the author after the competition.

### 1 **AAB to APK Conversion and Installation Process** :

#### 1.1 Change to apk:

We download the attachment we can wee app-release.aab ,According to information on the Internet , it's apk friend , which is the software package that install in Android . However, since the .aab file cannot be directly installed on a mobile device, I first used the [bundletool tool](https://github.com/google/bundletool) to convert the .aab file into .apks.

```cmd
java -jar bundletool-all.jar build-apks --bundle=app-release.aab --output=release.apks
```

Then, you can use the adb shell command to directly install the .apks file, or use the [MT Manager](https://mt2.cn/download/) to convert the .apks to an .apk file and then install it. Here I convert to apk

![image](assets/image-20250422201835-ybnma8i.png)

![image](assets/image-20250422201914-ykheyh2.png)

#### 1.2 Install the Apk file

After installation, it can run on the Android device.

![image](assets/image-20250422201954-klscpss.png)

The title above says "Press + for xx times?" There is a number displayed in the center of the page, and a "+" button at the bottom right corner. Therefore, it can be inferred that pressing the "+" button in the bottom right corner a certain number of times will trigger the appearance of the flag.I tried continuously clicking, and when the number reached 20, a fake flag appeared on the screen.

![image](assets/image-20250422202227-xdbzmvj.png)

## Method 1 :

### 2 **Static Analysis and Reverse Engineering Process** :

#### 2.1 **Parsing AndroidManifest.xml**

Based on previous CTF experience, it’s unlikely that simply clicking will yield the real flag. Therefore, it’s necessary to start reversing the application, beginning with the AndroidManifest.xml file. 

You can use tools like [Jadx](https://github.com/skylot/jadx) or MT Manager for this task. Since I am already using MT Manager, I chose to proceed with MT Manager.

![image](assets/image-20250422202606-wgn75db.png)

We can obtain some key information from the AndroidManifest.xml file. The launch activity is `com.example.calculator.MainActivity`​, as indicated by the `<action android:name="android.intent.action.MAIN" />`​ tag. Additionally, we can see `io.flutter.embedding.android.NormalTheme`​, which suggests that this application is developed using [Flutter](https://flutter.dev/).

#### 2.2 **DEX File Analysis :**

Therefore, we open the dex files to locate and see what information (such as classes and activities) can be found.

![image](assets/image-20250422202857-wyu6xli.png)

We can see that the dex files have been obfuscated by the author, but the package `com.example.calculator`​ is still clearly visible. So, we go inside to take a closer look.

![image](assets/image-20250422203019-51epnk2.png)

After opening it, we only see that it performs some initialization (init), but nothing valuable is found.Since the other classes are all obfuscated, I believe the author wouldn’t make it that straightforward for us :)

#### 2.3 **ELF File Analysis :**

![image](assets/image-20250422203314-ah507s6.png)

Therefore, I switched to IDA to open the libapp.so file and see if there’s anything interesting inside. At the same time, we also see the presence of libflutter.so, which further confirms that this application was developed using Flutter.

![image](assets/image-20250422203414-k3izap5.png)

#### 2.4 Find the Flag

#### 2.4.1 Search the Flag

I found that the .so file doesn’t contain any init or main functions, and I had no idea how to find the flag. Therefore, I switched to using the "Find String" function.

![image](assets/image-20250422203536-seh9p0m.png)

However, I only found the fake flag as well. , So I started thinking, maybe some other method was used to hide the flag? One of the most common ways is Base64 encoding, so I searched for ==

![image](assets/image-20250422203651-nwxlbym.png)

We found two results, so we checked both of them individually.

#### 2.4.2 Decode the Flag

I used [CyberChef](https://gchq.github.io/CyberChef/) to check whether they were Base64 encoded or not.

![image](assets/image-20250422203744-xdu4z8j.png)![image](assets/image-20250422203849-bz4zjm7.png)

We can see the second result turned out to be the flag.

PUCTF25{N0w_1_can_reverse_f1utt3r_f8eea2025180546f009d1a312f2d0ddb}

### 3 Conclusion:

* Download the AAB and use bundletool to convert it to APK and install.
* Initial interaction reveals a fake flag.
* Reverse engineer the AndroidManifest.xml, DEX, and SO files.
* Try various search and decoding methods; finally, obtain the flag by decoding with Base64.

## Method 2 :

The author mentioned that the real flag will be displayed on the screen after we click the button to reach -100. Therefore, I thought we could manipulate the dynamic memory to achieve this.

First, we need a rooted Android device. In this case, I used [LDPlayer 9](https://www.ldplayer.tw/?from=en). Next, we need to download a memory modifier; I chose [GameGuardian](https://gameguardian.net/forum/files/file/2-gameguardian/) for this purpose. After that, go to the settings in LDPlayer, enable root permissions, and then install GameGuardian.

![image](assets/image-20250426102752-78n3v5h.png)

After that, click the GameGuardian icon and you will see its user interface.

![image](assets/image-20250426102829-gw039gb.png)

Next, we need to select the target process.

If the flag is shown when the number reaches -100, my idea is to modify the memory value that stores the number to -101. Then, when we click the "+" button, the value will become -100, which should trigger the flag to update.

![image](assets/image-20250426103111-iqt2jf1.png)

The current number is 0, so we start by searching for the value 0.

![image](assets/image-20250426103144-2ja9rb7.png)

![image](assets/image-20250426103201-kvmi5g0.png)

![image](assets/image-20250426103213-4erh7w5.png)

There are a lot of results, so we click the add button to change the value to 1, and then refine for 1.

![image](assets/image-20250426103300-jxljvic.png)

Repeat this process until the number of results decreases.

![image](assets/image-20250426103641-zib8i7k.png)

After a few repetitions, there are only 4 results left.

So, we click the edit button and change the value to -101 :

![image](assets/image-20250426103654-4esr61u.png)

![image](assets/image-20250426103705-x13vxs8.png)

![image](assets/image-20250426103717-03f7i24.png)

![image](assets/image-20250426103730-vua65h7.png)

Then, we click the "+" button.

![image](assets/image-20250426103740-7udjd7s.png)

We successfully get the flag.

‍
