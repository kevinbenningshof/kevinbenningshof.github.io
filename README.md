# kevinbenningshof.nl #

This is the Git repository for the website **kevinbenningshof.nl**, which is currently using the Hugo CMS platform.

This website was migrated from the **mjcb.io** domain in early 2025.

## Theme ##

The website **kevinbenningshof.nl** uses the theme [Hugo Clarity](https://github.com/chipzoller/hugo-clarity).

## File and Folder Structure ##

```
.
├── /
│   ├── content/
│   │   ├── blog/
│   │   │   └── 00000-title.md
│   │   ├── icons/
│   │   │   └── icon.png
|   |   ├── images/
│   │   │   └── 00000-title/
|   |   |       └── title.png
|   |   ├── posts/
│   │   │   └── 00000-post.md
│   │   ├── about.md
│   │   ├── archives.md
│   │   └── search.md
│   ├── layouts/
│   │   ├── hooks/
│   │   │   └── head-end.html
│   ├── static/
|   |   ├── images/
|   |   ├── logos/
│   └── themes/
|       └── hugo-clarity/
├── hugo.toml
├── LICENSE
└── README.md


### Important Files ###

* **/hugo.toml** - Primary configuration file for Hugo. It is in TOML format.
* **/README.md** - This README file.

### Important Folders ###

* **/content/blog/** - Contains all blog posts. These are instructions and guidelines for IT related posts.
* **/content/icons/** - Contains all blog and post icons that are used as thumbnail.
* **/content/images/** - Contains the images for blogs and posts.
* **/content/posts/** -  Contains all posts that are created for personal information.
* **/layouts/partials/** - Contains all partial templates which are added to the existing theme.
* **/layouts/shortcodes/** - Contains all custom shortcodes that can be used.
* **/static/** - Contains all content that is placed in the root of the website when rendered by Hugo.
* **/static/docs/blog/** - Contains all linked documents for blog posts. Directories are numbered to match the blog post.
* **/static/gdpr-notice/** - Contains the necessary files for the GDPR notice for the website (if required).
* **/static/images/** - Contains all images.
* **/static/images/blog/** - Contains all images for blog posts. Directories are numbered to match the blog post.