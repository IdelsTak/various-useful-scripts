# Set `JAVA_HOME`

Check if you have Java installed and determine the correct `JAVA_HOME` path. You can do this by running the following command:

```shell
echo $JAVA_HOME
```
If this command doesn't return anything, it means `JAVA_HOME` is not currently set.

For example, if you're using the Bash shell, you can edit the `~/.bashrc` file using a text editor like nano or vi:

```shell
nano ~/.bashrc
```
Add the following line at the end of the file, replacing `/path/to/your/java/home` with the actual path to your Java installation directory:

```shell
export JAVA_HOME=/path/to/your/java/home
```
Save the file and exit the text editor.

To apply the changes immediately, you can either log out and log back in or run the following command in your terminal:

```shell
source ~/.bashrc
```
Verify that `JAVA_HOME` has been set correctly by running:

```shell
echo $JAVA_HOME
```


