Chirpy Starter Gem Version GitHub license
When installing the Chirpy theme through RubyGems.org, Jekyll can only read files in the folders /_data, /_layouts, /_includes, /_sass and /assets, as well as a small part of options of the /_config.yml file from the theme's gem. If you have ever installed this theme gem, you can use the command bundle info --path jekyll-theme-chirpy to locate these files.

The Jekyll team claims that this is to leave the ball in the user’s court, but this also results in users not being able to enjoy the out-of-the-box experience when using feature-rich themes.

To fully use all the features of Chirpy, you need to copy the other critical files from the theme's gem to your Jekyll site. The following is a list of targets:

.
├── _config.yml
├── _plugins
├── _tabs
└── index.html
To save you time, and also in case you lose some files while copying, we extract those files/configurations of the latest version of the Chirpy theme and the CD workflow to here, so that you can start writing in minutes.

Prerequisites
Follow the instructions in the Jekyll Docs to complete the installation of Ruby, RubyGems, Jekyll and Bundler.

Installation
Use this template to generate a brand new repository and name it <GH_USERNAME>.github.io, where GH_USERNAME represents your GitHub username.

Then clone it to your local machine and run:

$ bundle
Deploying locally
bundle exec jekyll s -l
-l means LiveReload => After save changes the site is regenerated and the browser refreshed
--drafts Render posts in the _drafts folder
--unpublished Render posts that were marked as unpublished
--help Show all options available
The content can be seen at Local

Usage
Please see the theme's docs.

<script src="https://tryhackme.com/badge/1603402"></script>
License
This work is published under MIT License.
