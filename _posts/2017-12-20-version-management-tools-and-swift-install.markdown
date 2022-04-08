Tools like RVM, RBenv, Pyenv, NVM and Swiftenv are invaluable when working with different projects. Wayne Seguin was the guy behind RVM, it literally makes Ruby development using the latest or multiple versions simple. You can switch between Rubinius, Ruby and JRuby with ease.

After install, just create a .ruby-version with the name of the Ruby version you want and a .ruby-gemset file with a name for a group of Gem dependencies, if you cd in and out of the directory and switch ruby versions manually using rvm use ruby-1.9.2 for example, when you switch back into the directory it will select the Ruby version for you.

Using the swift versions manager swiftenv is a little trickier, here are some instructions on how to set up a new install of the Swift programming language on Ubuntu 16.04 with the required dependencies:

```console
sudo apt-get install clang libicu-dev git cmake ninja-build


Install switfenv 
mkdir .swiftenv



git clone https://github.com/kylef/swiftenv ~/.swiftenv 

echo 'export SWIFTENV_ROOT="$HOME/.swiftenv"' >> ~/.bashrc 

echo 'export PATH="$SWIFTENV_ROOT/bin:$PATH"' >> ~/.bashrc 

echo 'eval "$(swiftenv init -)"' >> ~/.bashrh
```


Install Swift 4

Instructions for other versions: 
```console
https://github.com/kylef/swiftenv/blob/master/docs/commands.md 
You may need other dependencies like:
python uuid-dev libicu-dev icu-devtools libbsd-dev libedit-dev libxml2-dev 
libsqlite3-dev swig libpython-dev libncurses5-dev pkg-config lldb-4.0 llvm
cmark, llbuild
swiftenv install --verbose 4.0
or 

swiftenv install https://swift.org/builds/swift-4.0.3-release/ubuntu1604/
swift-4.0.3-RELEASE/swift-3.0.1-RELEASE-ubuntu16.04.tar.gz
```