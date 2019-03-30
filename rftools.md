
### Anaconda Package Manager setup

1. Setup Anaconda python environment by downloading and installing Miniconda.
    * https://docs.conda.io/en/latest/miniconda.html
    * Once downloaded, install anaconda to _$HOME/.anaconda_ directory
2. Create an Anaconda environment for gnuradio and other RF reverse
engineering tools by running `conda create -n rftools  python=2.7.16` command.
3. You can list all available environments with `conda env list` command.
4. Activate anaconda environment with `conda activate rftools` command.
5. You should now have a custom python environment running. Packages can be
installed through `pip install PACKAGE_NAME` or `conda install PACKAGE_NAME`
commands.

### Building QWT libraries from source on OSX environment
__NOTE!!!__ _activate appropriate conda environment before configuring and building
gnuradio!_
1. Download QWT from sourceforge website
2. Untar compressed file containing source
3. Run following command to generate Makefile `qmake qwt.pro -o Makefile`
4. Build and link QWT using `make CXXFLAGS+=-stdlib=libc++ MACOSX_DEPLOYMENT_TARGET=10.9`
5. Install library with `sudo make install`
6. Depending on where QWT was installed, library and include paths must be
   updated in GNURadio source. Edit `cmake/Modules/FindQwt.cmake` by adding
   appropriate QWT include and libary paths in script.
7. Create symbolic links of dynamic library files of
   `ln -s <QWT_INSTALL_PATH>/lib/*.dylib /usr/local/lib`

### Building GNURadio from source on OSX environment

__NOTE!!!__ _activate appropriate conda environment before configuring and building
gnuradio!_

1. Clone GNURadio repository located on GitHub page by issuing following command:
    * `git clone https://github.com/gnuradio/gnuradio.git`
2. Navigate to cloned gnuradio directory and switch to latest v3.7 branch:
    * `git checkout v3.7.13.4`
3. Clone VOLK repository inside gnuradio directory: 
    * `git clone https://github.com/gnuradio/volk.git`
4. Create a directory named _build_ and navigate to it.
5. Now build gnuradio binary from source with the following (Note you might need to install extra packages to enable certain components)
    * Following packages should installed using Homebrew:
        * `brew install atk cairo gdk-pixbuf gobject-introspection gtk+ harfbuzz pango py2cairo pygobject pygtk`
    * Following need to be installed using pip and conda:
        * Cheetah   - `conda install cheetah`
        * PyGObject - `pip install PyGObject`
        * QWT       - `pip install PythonQWT`
        * cairo     - `conda install cairo`
        * gtk2      - `conda install -c mw gtk2`
        * lxml      - `conda install lxml`
        * mako      - `conda install mako`
        * numpy     - `conda install numpy`
        * pygtk     - `conda install pygtk`
        * pyqt      - `conda install pyqt=4.11.4`
        * qt        - `conda install qt=4.8.7`
        * six       - `conda install six`
        * sphynx    - `conda install sphynx`
        * swig      - `conda install swig`
        * zeromq    - `conda install zeromq`
    * Create a gnuradio.pth file inside `$CONDA_PREFIX/lib/python2.7/site-packages` and store locations of libraries installed by Homebrew that don't get picked up by Anaconda:
        * `/usr/local/lib/python2.7/site-packages/gtk-2.0/`
        * `/usr/local/lib/python2.7/site-packages/`
    * Build environment by using `cmake -DPYTHON_EXECUTABLE=$CONDA_PREFIX/bin/python2.7 -DPYTHON_INCLUDE_DIR=$CONDA_PREFIX/include/python2.7 -DPYTHON_LIBRARY=$CONDA_PREFIX/lib/libpython2.7.dylib -DCMAKE_INSTALL_PREFIX=$CONDA_PREFIX/ ../`
    * Compile and link executables by running `make`
    * Install GNURadio by running `make install`
6. You should now have a fully functional GNURadio library along with enabled components (Qt and gnuradio-companion components should be enabled by now)

### Building and Installing osmocom GNURadio Blocks
1. Install hackRF library: 
    * `brew install hackrf`
    * `pip install pyside2`
    * `conda install ipython`
1. Clone osmocom repository:
    * `git clone git://git.osmocom.org/gr-osmosdr`
2. Edit CMakeLists.txt file and make following changes:
    * Line ~61: Change `IF(CMAKE_CXX_COMPILER MATCHES ".*clang")` to `IF(CMAKE_CXX_COMPILER MATCHES ".*clang" OR CMAKE_CXX_COMPILER MATCHES ".*c\\+\\+")`
    * Line ~66: Add new definition `ADD_DEFINITIONS(-std=c++11)`
2. Create a directory named _build_ inside osmocom directory and navigate to it.
3. Build from source and install by issuing following commands:
    * `cmake -DPYTHON_EXECUTABLE=$CONDA_PREFIX/bin/python2.7 -DPYTHON_INCLUDE_DIR=$CONDA_PREFIX/include/python2.7 -DPYTHON_LIBRARY=$CONDA_PREFIX/lib/libpython2.7.dylib -DCMAKE_INSTALL_PREFIX=$CONDA_PREFIX/ ../`
    * Compile and link executables with command `make`
    * Install GNURadio with command `make install`

### Installing rfcat
1. Clone rfcat repository:
    * `git clone https://github.com/atlas0fd00m/rfcat.git`
2. Install following python packages
    * future - `conda install future`
    * usb - `pip install pyusb`


### GNURadio Configuration and Troubleshooting

__Q__: Font is too small on Retina Display Macbooks?<br />
__A__: Increase font by adding following setting in  _$HOME/.gnuradio/config.conf_:
* `[grc] canvas_font_size = 13`

__Q__: Fixing `ImportError: No module named _osmosdr_swig` error?<br />
__A__: Append osmocom library location (`$CONDA_PREFIX/lib/python2.7/site-packages/osmosdr`) to _gnuradio.pth_ file inside `$CONDA_PREFIX/lib/python2.7/site-packages`

__Q__: Fixing `Symbol not found: __ZN5boost6system6detail20generic_category_ncxEv` error?<br />
__A__: Remove any libboost library links inside the __$CONDA_PREFIX__ directory. Also, make sure to remove any files that are left over from previous gnuradio/osmosdr installation

__Q__: Class QCocoaPageLayoutDelegate is implemented in both /Users/laskovasr/.anaconda/envs/rftools/lib/libQtGui.4.8.7.dylib (0x1083c62e0) and /usr/local/opt/qt/lib/QtPrintSupport.framework/Versions/5/QtPrintSupport (0x1212f2478). One of the two will be used. Which one is undefined. <br />
__A__: Two or more instances of Qt library found. Remove duplicates.
