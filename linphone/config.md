```c
    `git clone https://gitlab.linphone.org/BC/public/linphone-desktop.git --recursive`
    `cd linphone-desktop`
    `mkdir build`
    `cd build`
    `cmake .. -DCMAKE_BUILD_PARALLEL_LEVEL=10 -DCMAKE_BUILD_TYPE=RelWithDebInfo`
    `cmake --build . --parallel 10 --config RelWithDebInfo`
    `cmake --install .`
    `./OUTPUT/bin/linphone --verbose` or `./OUTPUT/Linphone.app/Contents/MacOS/linphone --verbose`

```

