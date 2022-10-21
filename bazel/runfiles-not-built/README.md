# Runfiles not built

> This issue seems to only affect Windows, presumably because `sh_binary` outputs several files on Windows and only one file on Linux/Mac.


On Windows, `sh_binary` outputs two files, a `.exe` file and the script provided in the `srcs` attribute. On Linux/Mac, `sh_binary` simply outputs a symlink to the script provided in the `srcs` attribute.
To be able to use a `sh_binary` like `$(execpath :my_sh_binary)` rather than `$(execpaths :my_sh_binary)`, this repo wraps a `sh_binary` in a `native_binary`. For this to work, a copy of the input shell script must be provided
 that matches the `native_binary` output exe without the `.exe` extension, hence the `copy_file` target in this repo, otherwise the resultant .exe hangs forever.

The issue is that when building `:sh_bin_wrapper_consumer`, the runfile of `:sh_bin` is not built, as such, `bazel run :sh_bin_wrapper_consumer` hangs forever due to the `sh_binary` output .exe cannot find the bash script to run.
The build of `:sh_bin_wrapper_consumer` is  only successful after first building `:sh_bin_wrapper` manually with `bazel build :sh_bin_wrapper`.

TL;DR - Transitive runfiles are not being built when building `:sh_bin_wrapper_consumer`.

