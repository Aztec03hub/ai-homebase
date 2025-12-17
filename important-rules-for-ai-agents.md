
**Script filenames should always be completely lowercase, unless explicitly directed otherwise**

**Always put the script filename at the top of the file in a comment, using underscores `_` to separate whole words:**
```powershell
# some_script_filename.ps1
```


**When trying to give powershell script instructions which modify or create `.ps1` files, the following escape syntax should be used:**

```powershell
# some_script_filename.ps1
# Use single-quotes @ escape syntax for escaping multi-line powershell script code:
@'
# Whatever code with $Var or ${Other_Var} doesn't need to be escaped line-by-line
'@
```
