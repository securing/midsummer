# Writeup structure

Before we move on to actual challenges, I should explain how the following writeup is structured.

## Major vs Minor task

Each **Stage** (formally known as 'Task' on TryHackMe) is divided into **Tasks** (formally knows as 'Questions') that you need to complete in order to gain points. 

It's important to note that you <ins>don't need to finish all tasks</ins> before you can unlock the next stage. The only prerequisite for a given Stage (if any) is the access to particular user account.

All stages contain at least 1 task which requires you to find your way into someone's account in order to complete it. Other tasks simply test your understanding of the topic or guide you to successfully find the Attack Vector and exploit it.

Thus, I decided to make the following distinction:
- **Major Task** is a task that requires you to find a flag stored on user account (which is equivalent to hacking your way into a new account)
- **Minor Task** is a task that doesn't meet Major Task requirements (meaning it might or might not require access to a new account)

## Stage breakdown
Article explain every stage will be divided with following sections in mind

### Exploitation

This section is devoted to the thought process behind finding and exploiting the vulnerability in question. It is divided into 4 sub-sections:

- **Briefing** - contains information about the task, straight from the CTF website
- **Reconnaissance** - looking for the Attack Vector to exploit
- **Attack Vector** - description of the found vulnerability
- **Execution** - using the Attack Vector to gain access into the user account

This section explains the <ins>Major Task only</ins>!

### Prevention

This section talks about the weakpoint of the system, how the vulnerability we previously used came to life and how to prevent it from appearing in the first place

### Minor tasks

This section explains how to complete the remaining Minor Tasks in the current Stage