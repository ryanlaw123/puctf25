# NuttyBot

NuttyBot is a our Best Friend. He is a very helpful bot. He is always there to help us.

Author: Sunny

Flag Format: PUCTF25{[a-zA-Z0-9_]+_[a-fA-F0-9]{32}}

---

### 1 Introduction

During the challenge, we interacted with an AI system that was capable of executing Python code. We leveraged this feature to retrieve the contents of the flag file.

### 2 Executing System Commands

First, we asked the AI to use python run ls to list all files in the current directory:

![image](assets/image-20250425122318-6ko4djf.png)

The output included a file named similar to `flag_a5xxxxxxxxxxx.txt`​.

After identifying the flag file, we instructed the AI to cat the file. The AI executed the command and displayed the flag.
