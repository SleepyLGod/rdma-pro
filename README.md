# rdma-pro (will be updated after submitting the paper...)

+ 🌃 Interfaces for RDMA access.
+ 🌃 Requires: C++ compiler with C++20 standard support and `libibverbs` development headers installed.
+ 🌃 Requires g++(gcc) 10 or later.

### Run

```bash
# clone the project with c++ support
git clone https://github.com/SleepyLGod/rdma-pro
cd rdma-pro

# install pre-commit
pip install pre-commit
# them:
pre-commit install

# install your .clang-format configuration
# eg:
# sudo apt-get install clang-format
# or:
# python -m pip install clang-format
# or config in your vscode or clion IDE

# build
cmake -Bbuild -DCMAKE_BUILD_TYPE=RelWithDebInfo -DCMAKE_INSTALL_PREFIX=$INSTALL_DIR .
cmake --build build
# to install
cmake --install build
```
 
