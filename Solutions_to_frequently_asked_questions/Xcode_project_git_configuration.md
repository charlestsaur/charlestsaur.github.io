# Xcode Project Git Configuration

## Source Control

In the top menu bar, click **Xcode → Settings... → Source Control**

There will be **Author Name** and **Author Email** here. These two will be displayed in the git commit record, so set them in advance.

There is also an **Ignored Files**.

It is used to specify which files or folders should not be added to the Git repository. It should come with:

```plaintext
*~
.Ds_Store
```

But we don't need to care about it.

Because it is written in `.git/info/exclude`, it cannot be put into the Git repository.

`.git/info/exclude` is only local.

So we won't use it.

But don't delete the contents.

Because when you create a new project in Xcode, if you select **Create Git repository on my Mac**, Xcode will automatically commit.

At this time, `.git/info/exclude` is useful.

After it is built (that is, after the first automatic commit of Xcode is completed).

We can add the `.gitignore` file, and after configuring the `.gitignore` file, delete the contents in `.git/info/exclude` (it doesn't matter whether it is deleted or not).

## Configuration

Here we take a C++ project as an example:

Open **Xcode** and click **Create New Project...** In the Choose a template for your new project: interface that pops up, select **macOS**, then select **Command Line Tool**, and click **Next** to continue.

**Product Name** can be anything, such as CPP, but please note that only uppercase or lowercase English letters, numbers and `-` can be used here (you can see the **Bundle Identifier**: below, and any other characters you fill in will become `-` ).

**Team** can be ignored.

For **Organization Identifier**, just fill in `com.local` , of course, you can fill in uppercase or lowercase English letters, numbers and `-` (if it starts with a number, a `-` will be added at the beginning).

**Organization Identifier** is used to generate Bundle Identifier, which is just a format requirement. It should not be used when doing C++ command line projects.

**Language** is unnecessary. We are going to do C++ projects anyway, so just fill in **C++**.

After filling in these, click **Next** It will ask you to select a directory, and it will generate your project root directory in the directory you selected.

(Remember to select **Create Git repository on my Mac**; it is also OK to not select it, and you can manually create the Git repository in the project root directory later).

For example, I will select `~/Documents/Xcode` as an example. After selecting it, click **Create** and it will generate your project root directory in the Xcode directory:

(If the name you wrote in Xcode is abc)

```zsh
~/Documents/Xcode ❯ tree
.
├── CPP
│   ├── CPP
│   │   └── main.cpp
│   └── CPP.xcodeproj
│       ├── project.pbxproj
│       ├── project.xcworkspace
│       │   ├── contents.xcworkspacedata
│       │   ├── xcshareddata
│       │   │   └── swiftpm
│       │   │       └── configuration
│       │   └── xcuserdata
│       │       └── abc.xcuserdatad
│       │           └── UserInterfaceState.xcuserstate
│       └── xcuserdata
│           └── abc.xcuserdatad
│               └── xcschemes
│                   └── xcschememanagement.plist
```

OK, now your project is created.

If you just selected **Create Git repository on my Mac**, please create a new `.gitignore` file.

And write in `.gitignore`:

```plaintext
.DS_Store
*.log

# Xcode user-specific files
*.xcodeproj/xcuserdata/
*.xcworkspace/xcuserdata/
UserInterfaceState.xcuserstate

# Xcode compilation products
DerivedData/
build/

# Xcode old version temporary files
*.mode1v3
*.mode2v3
*.perspectivev3
*.pbxuser

# Compilation products
*.o
*.obj
*.out
*.exe
```

Then submit.

This will make the Git repository cleaner.

* If you did not select **Create Git repository on my Mac** before, please run `git init` in the project root directory after creating `.gitignore` and submit everything in the file.

Then you can delete the content in `.git/info/exclude` (it doesn't matter whether you delete it or not).

OK, now your Xcode project's git is basically set up.