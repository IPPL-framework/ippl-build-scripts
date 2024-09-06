
# Overview

The IPPL Toolchain Builder (ITB) consists of shell scripts designed for downloading, compiling, and installing all of IPPL's dependencies. Their behavior can be configured using environment variables if the default settings are undesired.

## Environment Variables

The ITB scripts can be controlled using the environment variables summarized below:

| Variable | Default Value | Description |
|---       |---            |---          |
| ITB_DOWNLOAD_DIR | `$PWD` | The directory in which tarballs should be downloaded. |
| ITB_SRC_DIR | `$PWD` | The directory in which source files should be stored. |
| ITB_PREFIX | `$PWD` | The install prefix for the dependencies and the final build. |

# Kokkos Installer

The Kokkos script `100-build-kokkos` downloads and compiles Kokkos 4.1.00.

## Environment Variables

| Variable | Default Value | Description |
|---       |---            |---          |
| build_type | serial | Sets the name of the build. If the name is `cuda`, `openmp`, or `serial`, the appropriate backends will be automatically enabled. |
| NJOBS | 16 | Number of threads to use when compiling. |
| compute_capability | AMPERE80 | The GPU architecture for which Kokkos should compile. |
| compiler | Path to `gcc` | Sets the compiler to be used. If CUDA is enabled, this variable is overwritten to use Kokkos' wrapper for NVCC. |
| NVCC_WRAPPER_DEFAULT_COMPILER | The value of `$compiler` | Sets the host compiler for Kokkos' NVCC wrapper. |
| CUDA | OFF | Enable CUDA. |
| OPENMP | OFF | Enable OpenMP. |
| SERIAL | OFF | Enable serial. |

# heFFTe Installer

The heFFTe script `200-build-heffte` downloads and installs the current master branch of heFFTe (or a chosen release version).

## Environment Variables

| Variable | Default Value | Description |
|---       |---            |---          |
| backend | fftw | The backend to be used for FFTs. |
| build_type | The value of `$backend` | Sets the name of the build. If the name is `cuda`, `openmp`, or `serial`, the appropriate backends will be automatically enabled. |
| CUFFT | OFF | Enable cuFFT. |
| FFTW | OFF | Enable FFTW. |
| MKL | OFF | Enable MKL. |

# IPPL Installer

The IPPL script `300-build-ippl` builds IPPL using the dependencies with the appropriate settings.

## Environment Variables

| Variable | Default Value | Description |
|---       |---            |---          |
| build_type | serial | Sets the name of the build. If the name is `cuda`, `openmp`, or `serial`, the appropriate backends will be automatically enabled. |
| ENABLE_FFT | ON | Enable FFTs. |
| ENABLE_SOLVERS | ON | Build the solvers module. |
| ENABLE_ALPINE | ON | Build the ALPINE miniapps. |
| ENABLE_UNIT_TESTS | ON | Build the unit tests. |
| build_mode | Release | Set the build mode. |
| compiler | Path to `gcc` | Set the compiler to use for host code compilation. |


# All-in-One Wrapper

The `999-build-everything` script is an all-in-one wrapper install script for building IPPL and its dependencies. It accepts various command line arguments for customizing the exact bulid configuration.

The following command will build IPPL and both its external dependencies for both CUDA and OpenMP. The build and install directories will be named `build_mixed` and `Kokkos_mixed` (or `Heffte_mixed`) and everything will be stored in the current working directory. Kokkos will be compiled to optimize for Pascal 6.1 and compile commands will be generated for LSP support.

```sh
./build_recipes/999-build-everything -t mixed --enable-cuda --enable-openmp --kokkos --heffte --ippl --export --arch=PASCAL61
```

## Flags

Short form arguments are preceded by one dash (e.g. `-h`) and are one character long. Long form arguments are preceded by two dashes (e.g. `--help`) and can have any length as long as they don't contain whitespace. Arguments to short form arguments are separated from the flag by a space (e.g. `-t openmp`) whereas arguments to long form arguments are separated from the flag by an equals sign (e.g. `--target=openmp`). The flags to the wrapper script are summarized in the table below.

| Short form | Long form | Takes Argument | Description |
|---         |---        |       ---      |---          |
| h | help | No | Show a help message and terminate. |
| t | target | Yes | Sets the name of the build. This is a **required** flag, as this name is used in the build directories in order to differentiate builds with different parameters. If the name is `cuda`, `openmp`, or `serial`, the preset options for these build targets will be chosen. This value is stored as `$build_type`. |
| N/A | enable-cuda | No | Enables the CUDA backend for Kokkos (heFFTe will be compiled with the cuFFT backend). |
| N/A | enable-openmp | No | Enables the OpenMP backend for Kokkos (heFFTe will be compiled with the FFTW backend). |
| N/A | enable-serial | No | Enables the serial backend for Kokkos (heFFTe will be compiled with the FFTW backend). |
| N/A | enable-mkl | No | Enables the MKL backend for heFFTe. |
| g | arch | Yes | Sets the GPU architecture for which Kokkos should optimize (default Ampere 80). The argument must be in all caps, e.g. PASCAL61. |
| d | debug | No | Sets the build mode to debug (the default is release mode). |
| N/A | mode | Yes | Sets the build mode to the provided mode. |
| k | kokkos | No | Configure and build Kokkos. |
| f | heffte | No | Configure and build heFFTe. |
| i | ippl | No | Configure and build IPPL. |
| c | nofft | No | Disable FFTs in IPPL. |
| u | nounit | No | Disable unit tests in IPPL. |
| N/A | export | No | Instuct CMake to generate `compile_commands.json` for this build for LSP support. |

## Environment Variables

In addition to the standard ITB variables, the wrapper script can be controlled by setting the following environment variables:

| Variable | Default Value | Description |
|---       |---            |---          |
| OPENMP | OFF | Enable OpenMP. |
| Kokkos_DIR | `$ITB_SRC_DIR/Kokkos/Kokkos_${build_type}` | Kokkos install directory. By default, it uses the ITB install prefix and the name of the build. |
| Heffte_DIR | `$ITB_SRC_DIR/Heffte/Heffte_${build_type}` | heFFTe install directory. By default, it uses the ITB install prefix and the name of the build. |

