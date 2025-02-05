# Building OR-Tools with CPLEX Support on Mac M1

This guide documents the steps to build OR-Tools with CPLEX support on a Mac M1 and install it in a Conda environment.

## 1. Clone the OR-Tools Repository
```sh
git clone https://github.com/google/or-tools.git
cd or-tools
```

## 2. Modify `FindCPLEX.cmake`
Edit the file `cmake/dependencies/FindCPLEX.cmake`:
- **Remove** the following line (around line 78):
  ```cmake
  message(FATAL_ERROR "CPLEX do not support Apple M1, can't find a suitable static library")
  ```
- **Add** the following lines to correctly set the CPLEX target properties:
  ```cmake
  set_target_properties(CPLEX::CPLEX PROPERTIES
      IMPORTED_LOCATION "${CPLEX_ROOT}/cplex/lib/arm64_osx/static_pic/libcplex.a")
  ```

## 3. Configure CMake
Open a shell and run:
```sh
export CPLEX_ROOT="/Applications/CPLEX_Studio2211"
export UNIX_CPLEX_DIR="$CPLEX_ROOT"
export LDFLAGS="-L$CPLEX_ROOT/cplex/lib/arm64_osx/static_pic -L$CPLEX_ROOT/cplex/lib/arm64_osx -lz"
export PATH="$HOME/.local/bin:$PATH"  # Ensure stubgen is found

cmake -S . -B build -DBUILD_DEPS:BOOL=ON -DUSE_CPLEX=ON -DCPLEX_ROOT="$CPLEX_ROOT" -DBUILD_PYTHON:BOOL=ON
```

## 4. Build OR-Tools
```sh
cmake --build build --config Release
```

## 5. Test the Virtual Environment
OR-Tools automatically installs into a virtual environment. Activate it:
```sh
source build/python/venv/bin/activate
```
Then, check if OR-Tools is installed and whether CPLEX is available:
```sh
python -c "import ortools.linear_solver.pywraplp; print('ORTools installed successfully!')"
```
You should see 'ORTools installed successfully!'.

Create a test.py file and copy this code:

```python
from ortools.linear_solver import pywraplp

solver = pywraplp.Solver.CreateSolver("CPLEX")
if solver is not None:
    print("CPLEX is available!")
else:
    print("CPLEX is NOT available.")
```

then in the shell:

```sh
python test.py
```

you should see 'CPLEX is available!'


## 6. Install OR-Tools in a Conda Environment
```sh
conda create -n Myenv python=3.11
conda activate Myenv
pip install build/python/dist/ortools-*.whl
```

## 7. Verify Installation in Conda
Try again the steps at point 5.

If everything works correctly, OR-Tools with CPLEX is now installed in the Conda environment!

## Notes
- Ensure `CPLEX_ROOT` is correctly set before running CMake.
- If you encounter linker errors, double-check the `LDFLAGS` export for proper library paths.
- The OR-Tools build process installs dependencies in `build/python/venv`, so testing inside the virtual environment first can help diagnose issues.