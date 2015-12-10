# ESPN WordPress local development setup
Brief description of the general architecture of the ESPN Wordpress install. There’s a lot here, don’t get overwhelmed.

## Email ESPNbuildeng@espn.com and request to be added to ESPN’s GitHub install
> Title: Request to be added to code.espn.com Github

> I work for SITENAME, which is owned by ESPN. My GitHub username is USERNAME. My ESPN email is EMAIL@ESPN.COM. I have two-factor authentication enabled. Please let me know if you need anything else to add me to the code.espn.com.

## Configure GitHub
Add your SSH key to GitHub: https://github.com/settings/ssh
Add your SSH key to ESPN GitHub: https://code.espn.com/settings/ssh
Enable two-factor authentication: https://github.com/settings/security
Add your ESPN email address to GitHub: https://code.espn.com/settings/emails
Set your ESPN email address to receive notifications: https://code.espn.com/settings/notifications

Get an authentication token from ESPN GitHub: https://code.espn.com/settings/tokens/new
When you’re prompted for a password during your first git clone call, use this token.

Make SSH work on the ESPN network. Add this to ~/.ssh/config:
Host github.com
  Hostname ssh.github.com
  Port 443
Install git
Download git (2.6.2).
Configure git
$ git config --global user.name "Jane Doe"
$ git config --global user.email "jane.doe@espn.com"
$ git config --global alias.st "status -s"
$ git config --global alias.br branch
$ git config --global alias.co checkout
Configure git autocomplete
$ curl -o ~/.git-completion.bash https://raw.githubusercontent.com/git/git/master/contrib/completion/git-completion.bash
$ echo 'source ${HOME}/.git-completion.bash' >> ~/.profile
Install git extras
$ (cd /tmp && git clone --depth 1 https://github.com/tj/git-extras.git && cd git-extras && sudo make install)
Git Repositories
Place all four in your development folder:
VIP QS	git clone --recursive git@github.com:Automattic/vip-quickstart.git
FTE		git clone git@code.espn.com:webdev/fivethirtyeight.git
GL		git clone git@code.espn.com:webdev/grantland.git
Plugins	git clone git@code.espn.com:webdev/plugins.git


Configure Vagrant
Place this in ~/.vagrant.d/Vagrantfile:
Vagrant.configure("2") do |config|
  config.vm.synced_folder "../theundefeated", "/srv/www/wp-content/themes/vip/espn-theundefeated"
  config.vm.synced_folder "../fivethirtyeight", "/srv/www/wp-content/themes/vip/espn-fivethirtyeight"
  config.vm.synced_folder "../grantland", "/srv/www/wp-content/themes/vip/espn-grantland"
  config.vm.synced_folder "../plugins", "/srv/www/wp-content/themes/vip/espn-plugins"

  config.vm.provider "virtualbox" do |v|
    v.memory = 2048
    v.cpus = 2
    v.customize ["modifyvm", :id, "--natdnshostresolver1", "on"]
    v.customize ["modifyvm", :id, "--natdnsproxy1", "on"]
    v.customize ["modifyvm", :id, "--nictype1", "Am79C973"]
    v.customize ["modifyvm", :id, "--ioapic", "on"]
  end
end

In  /dev/folder/vip-quickstart/Vagrantfile, replace this line:
config.vm.synced_folder ".", "/srv", owner: 'www-data', group: 'www-data', mount_options: ["dmode=775", "fmode=664"]
…with this line:
config.vm.synced_folder ".", "/srv", nfs: true

Then do git add Vagrantfile, git commit -m "enabled NFS for Quickstart" and git push.
Configure your hosts file
$ sudo echo '10.86.73.80 vip.local gl.vip.local tu.vip.local' >> /etc/hosts
Configure your sudoers file
Add this to the bottom of /etc/sudoers:
# Vagrant / VIP Quickstart
# See https://docs.vagrantup.com/v2/synced-folders/nfs.html
Cmnd_Alias VAGRANT_EXPORTS_ADD = /usr/bin/tee -a /etc/exports
Cmnd_Alias VAGRANT_NFSD = /sbin/nfsd restart
Cmnd_Alias VAGRANT_EXPORTS_REMOVE = /usr/bin/sed -E -e /*/ d -ibak /etc/exports
%admin ALL=(root) NOPASSWD: VAGRANT_EXPORTS_ADD, VAGRANT_NFSD, VAGRANT_EXPORTS_REMOVE
Install Vagrant
$ curl -O -L https://dl.bintray.com/mitchellh/vagrant/vagrant_1.7.4.dmg
$ hdiutil attach vagrant_1.7.4.dmg
$ sudo installer -target / -pkg /Volumes/Vagrant/Vagrant.pkg
$ hdiutil detach /Volumes/Vagrant
Install VirtualBox
$ hdiutil attach http://download.virtualbox.org/virtualbox/5.0.10/VirtualBox-5.0.10-104061-OSX.dmg
$ sudo installer -target / -pkg  /Volumes/VirtualBox/VirtualBox.pkg
$ hidutil detach /Volumes/VirtualBox
Install Node
$ curl -O https://nodejs.org/dist/v4.2.2/node-v4.2.2.pkg
$ sudo installer -target / -pkg node-v4.2.2.pkg
Install Grunt
$ sudo npm install -g grunt-cli
$ cd /dev/folder/fivethirtyeight
$ sudo npm install
$ cd /dev/folder/grantland
$ sudo npm install
$ cd /dev/folder/the-undefeated
$ sudo npm install
$ cd /dev/folder/plugins
$ sudo npm install
Install SASS
$ sudo gem install sass
Install PEAR (Yosemite) [source]
$ cd /usr/lib/php
$ sudo php install-pear-nozlib.phar
Install PEAR (El Capitan)
$ cd /tmp
$ curl -s -O http://pear.php.net/go-pear.phar
$ php go-pear.phar

You’ll see many lines of text, ending with:
1-12, 'all' or Enter to continue:

Type 1, then type /usr/local/pear and hit Enter.
1-12, 'all' or Enter to continue: 1
(Use $prefix as a shortcut for '/usr', etc.)
Installation base ($prefix) [/usr] : /usr/local/pear

Then hit Enter again. Add /usr/local/pear/bin/pear to your path.
Install PHP CodeSniffer
$ sudo pear install PHP_CodeSniffer
$ cd /dev/folder
$ git clone git@github.com:WordPress-Coding-Standards/WordPress-Coding-Standards.git
$ sudo phpcs --config-set installed_paths /dev/folder/WordPress-Coding-Standards
$ sudo phpcs --config-set colors true

If you see an include_once() error, be sure that your php.ini file exists (check php -i for the correct path) and has 
include_path=".:/usr/local/share/pear" ← Mavericks
include_path=".:/usr/lib/php/pear" ← Yosemite
include_path=".:/usr/local/pear" ← El Capitan                                                                                                                               
specified at the end of the file.

Handy aliases for your bash .profile:
alias vipcs='phpcs -p -s -v --colors --standard=/dev/folder/espn-fivethirtyeight/project.ruleset.xml'
alias vipcbf='phpcbf --standard=/dev/folder/espn-fivethirtyeight/project.ruleset.xml'
Fix Subversion (Yosemite) [source]
$ sudo ln -s /Applications/Xcode.app/Contents/Developer/Library/Perl/5.18/darwin-thread-multi-2level/SVN /System/Library/Perl/Extras/5.18/SVN
$ sudo ln -s /Applications/Xcode.app/Contents/Developer/Library/Perl/5.18/darwin-thread-multi-2level/auto/SVN/ /System/Library/Perl/Extras/5.18/auto/SVN

If you get this error:
'tempfile' can't be called as a method at /usr/local/git/lib/perl5/site_perl/Git.pm
…make sure to install the latest git (currently 2.6.2) and aren’t using an old git (such as 1.8.1.3).
Fix Subversion (El Capitan)
$ sudo mkdir /Library/Perl/5.18/auto
$ sudo ln -s /Applications/Xcode.app/Contents/Developer/Library/Perl/5.18/darwin-thread-multi‌-2level/SVN /Library/Perl/5.18/darwin-thread-multi-2level
$ sudo ln -s /Applications/Xcode.app/Contents/Developer/Library/Perl/5.18/darwin-thread-multi‌-2level/auto/SVN /Library/Perl/5.18/auto/
Start Virtualbox
$ cd vip-quickstart
$ vagrant up
Configure WordPress
Go to http://vip.local/wp-admin/ and log in (u/p: wordpress/wordpress)
Enable the FiveThirtyEight theme
Go to My Sites > Network Admin > Sites
Click Edit beside / (the default/only site)
Click Themes
Click Enable underearth FiveThirtyEight
Go to My Sites > vip.local > Dashboard
Go to Appearance > Themes
Hover over FiveThirtyEight
Click Activate

$ cd vip-quickstart
$ vagrant ssh
$ wp theme activate vip/espn-fivethirtyeight
Set a site title and permalink structure
Go to My Sites > Network Admin > Sites
Enter “FiveThirtyEight” as the title
Enter “local development” as the tagline
Scroll down and find permalink structure
Enter /%postname%/ in the text field
Click Save Changes

$ cd vip-quickstart
$ vagrant ssh
$ mysql -u root wordpress
mysql> delete from wp_options where option_name = 'rewrite_rules';
Seed the database
Add the Verticals taxonomy:
$ wp fivethirtyeight seed-data
Enable image taxonomies
Go to Settings > Taxonomy Images
Check Bugs
Click Save Changes
Configure home page and blog
Go to Settings > Reading
Beside Front Page Displays, click “A static page”
Select Front Page > Home
Select Posts page > DataLab
Click Save Changes
Notes
VIP Quickstart docs: https://vip.wordpress.com/documentation/quickstart/
Run bin/vip-init periodically to update your Quickstart instance
If you use the wp importer plugin, it needs to be patched: https://core.trac.wordpress.org/ticket/24373
If you use TextMate, install Strip WhiteSpace on Save and Whitespace 
Use tail -f /var/log/syslog | sed 's/#012/\n\t/g' to tail the system log and have newlines render correctly
To import data — note that importing via the GUI is disabled:
wp plugin activate --network wordpress-importer
wp import --authors=create some-file.001.xml --url=http://site.vip.local/
Configure your hosts file on the VM
$ sudo echo '127.0.0.1 vip.local gl.vip.local tu.vip.local' >> /etc/hosts
Troubleshooting
svn repository
Try a hard reset on your svn branch, just like you would with a regular git branch:
$ git reset --hard HEAD

If your svn repository gets out of sync or corrupted:
$ git checkout master
$ git branch -D svn
$ git checkout -b svn git-svn
$ git svn rebase
Adding git-svn to an existing git repository
add to .git/config:
[svn-remote "svn"]
    url = https://vip-svn.wordpress.com/espn-fivethirtyeight
    fetch = :refs/remotes/git-svn
$ git svn fetch svn -r HEAD
$ git co -b svn git-svn
Vagrant
If vagrant gets disassociated from your VM, reset the virtualbox ID:
$ VBoxManage list vms | grep quickstart
"vip-quickstart_default_1431365185830_12124" {50feafd3-74cd-40b5-a170-3c976348de27}

Take that UUID and use it to populate the ID file:
$ echo -n "50feafd3-74cd-40b5-a170-3c976348de27" >.vagrant/machines/default/virtualbox/id

If vagrant hangs on boot, enable the GUI in ~/.vagrant.d/Vagrantfile to help debug:
config.vm.provider "virtualbox" do |v|
  v.gui = true
end

If you see errors about connection timeouts:
==> default: Waiting for machine to boot. This may take a few minutes...
    default: SSH address: 127.0.0.1:2222
    default: SSH username: vagrant
    default: SSH auth method: private key
    default: Warning: Connection timeout. Retrying...
    default: Warning: Connection timeout. Retrying...
    default: Warning: Connection timeout. Retrying...

It’s likely due to Quickstart’s IP address (10.86.73.80) conflicting with ESPN’s ethernet and ecorp wifi networks, which also use 10.x addresses. Make sure you are not on either of these networks and Junos Pulse is disconnected when starting up the VM. You can connect to them after the VM boots.

If Vagrant displays a GuestAddtions warning on boot:
Got different reports about installed GuestAdditions version:
Virtualbox on your host claims:   4.3.30
VBoxService inside the vm claims: 5.0.4

Edit your vbox file, such as ~/VirtualBox\ VMs/vip-quickstart_default_1431365185830_12124/vip-quickstart_default_1431365185830_12124.vbox

Change the GuestProperty tags so they match the current version of VirtualBox:
<GuestProperty name="/VirtualBox/GuestAdd/Version" value="5.0.4" timestamp="1442455559415214000" flags=""/>
<GuestProperty name="/VirtualBox/GuestAdd/VersionExt" value="5.0.4" timestamp="1442455559415499000" flags=""/>
Layout
If the home page layout is wildly incorrect, make sure Settings > Reading has the correct pages chosen (see “Configure home page and blog”).
Local debugging
You can set up an SSH tunnel that acts as proxy and lets you view the site on your own machine. Add your SSH public key to ~/.ssh/authorized_keys on the VM. Then start the tunnel:
ssh -i ~/.ssh/id_rsa -L *:80:vip.local:80 -N vagrant@vip.local

This helps mobile debugging by making your Quickstart server available on your main IP address, which is accessible to your phone.
Chrome for iOS
To debug Chrome for iOS, use weinre. 
Set up the SSH tunnel (see above)
Install it with sudo npm -g install weinre
Run it with weinre --boundHost -all-
In Chrome on your desktop, go to http://localhost:8080/.
Drag the “weinre target debug” bookmarklet to your bookmarks bar.
Edit the bookmarklet, replacing “localhost” with your IP address (on the WiFi network).
In Chrome on your desktop, go to http://localhost:8080/client/.
In Chrome on iOS:
Go to http://x.y.z.a/ where x.y.z.a is the same IP address from step 6.
Tap …
Tap bookmarks
Tap “weinre target debug”
In Chrome on your desktop:
Under “Targets,” click the link corresponding to your session
Click Elements
