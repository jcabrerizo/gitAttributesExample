# gitAttributesExample

Example for configure and use [.gitattributes](https://git-scm.com/docs/gitattributes) for replace tokens using a [filter](https://git-scm.com/docs/gitattributes#_filter)
 
## Let's do it

1. Clone the repo, the `.gitattributes` file has been pushed. It will be shared with all the developers. It relates the file pattern for targeting the files where the filter will be applied
1. Create the filter in your `.git/config`
1. Do the change in the target files to have your personal data
1. Execute `git checkout <your_target_files>` to force the execution of the filter
 
Your files should now have your personal data but executing `git status` they don't have changes so the data won't be stashed
 
## Example

This repo contains a couple of directories and inside both a file named `config.json` containing a property for an authentication token. The objective is to be able to use your personal token in your system, but not to push it as part of your changes without manual intervention.

We need to apply a filter like this:
 
```
[filter "config"]
   clean = sed 's:<USER_TOKEN>:<TOKEN_PLACEHOLDER>:g'
   smudge = sed 's:<TOKEN_PLACEHOLDER>:<USER_TOKEN>:g'
```
 
The `<USER_TOKEN>` in the clean and smudge commands represents the personal, secret and not to share token. It is written inside the filter but this file won't be pushed to the repo. `<TOKEN_PLACEHOLDER>` is what lives in the code remotely and what we want to be replaced when we pull the changes. 

This example uses [sed](https://www.gnu.org/software/sed/manual/sed.html) for doing the replacement for both commands:
 
* clean: executed before look for differences from the previous commit in the target file
* smudge: execute after fetching the changes from the remote repo
 
After cloning this repo, and before you add the filter in you `.git/config`, you will see the `config.json` files the value "<REPLACE_TOKEN>" for the authToken key.

Add the filter to you config by executing this in the project root (or just put the line with the filter and the next two in the file manually)

```properties
cat << EOF >> .git/config
[filter "config"]
   clean = sed 's:thisIsMyUserToken:<REPLACE_TOKEN>:g'
   smudge = sed 's:<REPLACE_TOKEN>:thisIsMyUserToken:g'
EOF
```

Once you have added the example filter, **manually**, replace the token in both files with "thisIsMyUserToken" and execute 

```bash
git checkout module1/config.json module2/config.json
```

If you check now the files, they contain the fake token instead of the <REPLACE_TOKEN> mark, but running `git status` there are no changes, on those files, to be staged.
 
 ## Improvements / alternatives

* Keep the token in an environment variable instead of inside the config file. The `sed` expression needs to be escaped name with double quotes
* Instead of using sed, you can pass a script to be executed on their behalf, having more flexibility. Also, the scripts can be added to the project
* Use `-e` with `sed` for doing multiple replacements at once
