# gitAttributesExample

Example for configure and use .gitattributes for replace tokens
 
## Configuration

1. Clone the repo
1. Create the filter in your `.git/config`
1. Do the change in the target files to have your personal data
1. Execute `git checkout <your_target_file>` to force the execution of the filter
 
Your files should now have your personal data but executing `git status` they don't have changes so the data won't be stashed
 
## Example

This repo contains a couple of directories and inside a of both a file named `config.json` containing a property for an authentication token. The objective is to be able to use the token in your system, but not to push it as part of your changes without manual intervention.
 
```
[filter "config"]
   clean = sed 's:<USER_TOKEN>:<TOKEN_PLACEHOLDER>:g'
   smudge = sed 's:<TOKEN_PLACEHOLDER>:<USER_TOKEN>:g'
```
 
The token is written inside the filter, this case using `sed` for doing the replacement. The filter has to commands:
 
* clean: executed before look for differences from the previous commit in the target file
* smudge: execute after fetching the changes from the remote repo
 
After cloning this repo, and before you add the filter in you `.git/config`, you will see the `config.json` files the value "<REPLACE_TOKEN>" for the authToken key.

```properties
cat << EOF >> .git/config
[filter "config"]
   clean = sed 's:thisIsMyUserToken:<REPLACE_TOKEN>:g'
   smudge = sed 's:<REPLACE_TOKEN>:thisIsMyUserToken:g'
EOF
```

Once you have added the example filter, **manually**, replace the token in both files with "thisIsMyUserToken" and execute `git checkout module1/config.json module2/config.json`.
 
If you check now the files, they contain the token, but running `git status` there are no changes, on those files, to be staged.
 
 ## Improvements

* Keep the token in an environment variable instead of inside the config file. The `sed` expression needs to be escaped name with double quotes
* Instead of using sed, you can pass a script to be executed on their behalf, having more flexibility. Also, the scripts can be added to the project