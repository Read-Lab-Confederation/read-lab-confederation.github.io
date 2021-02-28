# EMERGENT Website
Development repository for the EMERGENT website

## Folder Overview
```
emergent-website/
├── archetypes
├── content
│   ├── blog
│   │   └── posts
│   └── group
├── data
│   └── group
├── layouts
├── static
│   ├── css
│   └── images
│       ├── badges
│       └── group
└── themes
```

### New Content Related Folders
The following folders are related to adding new content to the EMERGENT blog.

##### `content/blog/posts`
In this folder you can add new blog posts in Markdown format. Please see [previous posts](https://github.com/Read-Lab-Confederation/emergent-website/tree/master/content/blog/posts) for examples.

##### `content/group`
This folder contains profile information (in Markdown format) for each member of EMERGENT. The Markdown files should be named as follows: (`${firstname}-${lastname}.md`).

##### `data/group`
This folder contains JSON files for each member of EMERGENT. The JSON files contain variables related to social networks, names, etc. The JSON files should be named the same as the group Markdown files (`${firstname}-${lastname}.json`).

##### `static/images`
This folder is where you should place any images related to a blog post.


##### `static/images/group`
This folder is where you should place an image (either PNG or JPG) of yourself. The image should be named the same as the group Markdown files (`${firstname}-${lastname}.png` or `${firstname}-${lastname}.jpg`).


### Template/Layout Related Folders
The **archetypes**, **layouts**, and **themes** directories are for the layout of the blog. It should be very rare that you need to change anything in these directories. So, please feel free to ignore these directories.
