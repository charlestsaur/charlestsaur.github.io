# jenv

(This document is for macOS, ChatGPT is the co-author)

jenv ([https://github.com/jenv/jenv](https://github.com/jenv/jenv)) is a Java version manager that makes it easy to switch between Java versions.

📌 How does jenv work?

jenv works by changing the `JAVA_HOME` environment variable in your current shell to point to the Java version you want to use.

It doesn't uninstall, overwrite, or modify any existing Java installations.

When you add a Java version, jenv simply records its path — it doesn’t copy any files.

Switching versions only updates `JAVA_HOME`, like this:

```zsh
export JAVA_HOME=/Library/Java/JavaVirtualMachines/jdk-17.jdk/Contents/Home
```

jenv doesn't touch anything in `/usr/bin/java` or `/Library`, so it's safe and easy to manage.

## Installing jenv

1. Install the project first

(If you use Homebrew or MacPorts, you can install jenv directly — they'll also set up your shell configuration automatically)

Source code installation:

```zsh
git clone https://github.com/jenv/jenv.git ~/.jenv
```

(This example clones the repo into your home directory, but you can choose a different location if you prefer)

2. Depending on your shell, run the appropriate commands below to add jenv to your `PATH` and initialize it.

(This example uses the `home` directory — change the `PATH` if needed)

Bash user, execute:

```zsh
echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.bash_profile
echo 'eval "$(jenv init -)"' >> ~/.bash_profile
source ~/.bash_profile
```

zsh user, execute:

```zsh
echo 'export PATH="$HOME/.jenv/bin:$PATH"' >> ~/.zshrc
echo 'eval "$(jenv init -)"' >> ~/.zshrc
source ~/.zshrc
```

1. Enable the export plugin (to enable automatic switching of JAVA_HOME)

```zsh
eval "$(jenv init -)"
jenv enable-plugin export
```

(More on this plugin in the Replenish below)

Then either restart your terminal or run:

```zsh
exec $SHELL -l
```

2. Prepare Java

It depends on how you want to install it. If you have it, don't install it.

3. Add Java to jenv management

Check the version of java you installed:

```zsh
/usr/libexec/java_home -V
```

Then add it to jenv

```zsh
jenv add "$(/usr/libexec/java_home)"
```

You can confirm the addition is successful with the following command:

```zsh
jenv versions
```

4. Set the default Java version

Set the global default version:

```zsh
jenv global 21.0.2
```

(You can also not set it and continue to use the system set version)

Set the local version of a project directory:

```zsh
cd /path/to/project
jenv local 21.0.2
```

Set the version only in the current shell session:

```zsh
jenv shell 21.0.2
```

5. Verify that JAVA_HOME is effective

```zsh
echo $JAVA_HOME
```

It should point to something like ~/.jenv/versions/21.0.2

-***Extra Tips***-

After installation, run:

```zsh
jenv doctor
```

You can check if jenv is working properly

If you use fish shell, the configuration method is slightly different. For details, please refer to the official jenv documentation

## Replenish

### eval

`eval` is a command in Linux/macOS shell. Its function is to execute the string content in the parameter as a command

For a simple example:

```zsh
eval "echo hello"
```

It is equivalent to directly executing:

```zsh
echo hello
```

In the sentence you said:

```zsh
eval "$(jenv init -)"
```

It means:

First execute the command `jenv init -` and take out its output (usually a string of shell commands, such as setting environment variables, defining functions, etc.)

Then use `eval` to execute this string of output commands to complete jenv Initialization configuration

Why do this?

Because `jenv init -` actually outputs commands to be written into the shell configuration, such as:

```zsh
export PATH="$HOME/.jenv/bin:$PATH"
export some_other_variable=...
# And define some functions, etc.

```

With `eval "$(jenv init -)"`, you don't have to copy and paste manually, the shell will automatically execute all the initialization steps it outputs

Brief summary:

`eval`: Execute parameters as code

`jenv init -`: Output the shell commands required to initialize jenv

`eval "$(jenv init -)"`: Execute the jenv initialization command to make jenv effective

If you want to see it more intuitively, try it first:

```zsh
jenv init -
```

You will see that it outputs a bunch of shell commands. Executing `eval "$(jenv init -)"` is to let the shell Run these commands

### export

`jenv enable-plugin export` is used to enable a plugin function of jenv, called `export` plugin

What does it do?

* By default, jenv only helps you switch Java versions, but does not automatically modify the environment variable `JAVA_HOME`

* Many programs rely on the environment variable `JAVA_HOME` to find the correct Java path

What does the export plugin do?

* It automatically sets and updates the `JAVA_HOME` environment variable for you, so that your terminal and applications can use your currently selected Java version

* After enabling, you switch Java versions (such as using `jenv global 17`), and `JAVA_HOME` will automatically change accordingly

For a simple example:

Before enabling the export plugin:

```zsh
jenv global 17
echo $JAVA_HOME
# It may be empty or an old version path
```

After enabling:

```zsh
jenv enable-plugin export
jenv global 17
echo $JAVA_HOME
# Displays a path like /Users/yourname/.jenv/versions/17
```