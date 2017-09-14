Fallback for YunoHost
==================

Fallback is a special app, only by command line interface, which provide a way to have a secondary server which you can used if your main server goes down.  
This other server will allow you to deploy a copy of your server to bring back you to internet during your break down.

More information below.

[Yunohost project](https://yunohost.org/#/)

https://github.com/maniackcrudelis/Fallback-server

**Upgrade this package:**  
`sudo yunohost app upgrade fallback -u https://github.com/YunoHost-Apps/fallback_ynh`

**Package state:**  
*[Last continuous integration test](https://ci-apps.yunohost.org/jenkins/job/fallback%20%28Community%29/lastBuild/consoleFull)*

---

## How it works

This app is made of 2 parts, you have to install it with the `main server` option on your main server, and again, with the `fallback server` option on the other server you want to use as rescue server.
> You should keep this other server only for that purpose. And maybe also to make some other backups. And it's obviously better if you can have this other server in another place that the previous one.

After the installation, every night, the main server will make some backups and send them to your fallback server.  
At least, it'll make a backup of your system. You can ask to backup your apps too, by editing the file `/home/yunohost.app/fallback/app_list`  
Before make any backup, it will compare with the last backup. And will make a new one, only if there any news on it. Just to preserve your bandwith.
> These backups will be encrypted, unless you ask to not.

If you have an issue with your main server, so you can use your fallback server and restore your nightly backups and run with this server during your break down.  
Let's see how this works.

#### What I have to do in case of break down of my main server ?

So, here we are, you have a failure with your main server. That had to happen !  
Don't worry, jump on your fallback server and use the deploy script.
`/home/yunohost.app/fallback/deploy_fallback`

This script will backup your fallback server first and then restore the backup from your main server.  
This restore will change your fallback server and give him the configuration of your main server, and restore the apps you've selected in your app_list.  
Now this server will became your main server, received your emails and run your apps.
> In fact, there just a "small things", you have to change your dns to indicate the new IP of your server. Let's see that at the end of this doc please.

#### So, now my main server is fixed. But I need to get all the stuff happened on the fallback.

If you're done with your fallback server, use the other script, just next to the previous one.
`/home/yunohost.app/fallback/close_fallback`

This script will backup your fallback system and the apps you've previously restored on it. And put all of that aside.  
Then, it will remove all of that of your system and restore the backup it made when you used `deploy_fallback`. Like that, nothing happenned on this server.

So, all your data produced on your fallback are now aside. Waiting for you.  
Return on your main server, and use this script.  
`/home/yunohost.app/fallback/update_from_fallback`

To finish, this script will get the backups from the fallback and restore them on your main server. To update your data and keep your main server up to date with what you've done on your fallback.  
To be more carefull, each restoration will be preceed by a backup. Just in case.

#### And... How I'm supposed to change my IP quickly in all the dns ?

That's a difficult question...  
And that needs more experimentation...  
But, there a way to do that simply. Use a dynamic dns entry at your registrar (if it allow it), even if you have a static ip. And update this ip if you have to switch to your fallback.  
Depend of your registrar, you should have a CLI tools to do that simplier.
