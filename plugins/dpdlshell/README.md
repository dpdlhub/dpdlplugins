# Dpdl plug-in


<p align="left">
	<img src="https://www.dpdl.io/images/dpdl-io_blue.png" width="35%">
</p>

				www.dpdl.io


## **`dpdlshell`**

The Dpdl plug-in **`dpdlshell`** allows to execute shell commands


**simple example:**

```python
println("executing some shell commands...")

string mydir_name = "./Test/my_data"
string myfile_name = "my_data_archive.tar.gz"

dpdl_stack_var_put("directory", mydir_name)
dpdl_stack_var_put("filename", myfile_name)

println("creating a an archive of my data...")

>>shell
	full_file_path=$mydir_name/$myfile_name

	tar -cvzf $full_file_path $mydir_name

	echo "data archive created: $?"

	exit $?
<<

int exit_code = dpdl_exit_code()

println("shell commands exit code: " + exit_code)
```


### Specification

The specification is found here:

[dpdlshell doc](https://www.dpdl.io/doc/dpdlplugins/dpdlshell/SPEC.md)





