# DiceCTF Quals 2024 misc/zshfrick
may your code be under par.
execute the `getflag` binary somewhere in the filesystem to win

Files: jail code

Target: IP + port to netcat into

Solves: 107


## Solution

This challenge was a zsh jail where you would connect and only be able to pick a character set of 6 to type commands with.  
We are also banned from using *, ?, and ` in our character set


Here is the code for the jail:
```zsh
#!/bin/zsh
print -n -P "%F{green}Specify your charset: %f"
read -r charset
# get uniq characters in charset
charset=("${(us..)charset}")
banned=('*' '?' '`')

if [[ ${#charset} -gt 6 || ${#charset:|banned} -ne ${#charset} ]]; then
    print -P "\n%F{red}That's too easy. Sorry.%f\n"
    exit 1
fi
print -P "\n%F{green}OK! Got $charset.%f"
charset+=($'\n')

# start jail via coproc
coproc zsh -s
exec 3>&p 4<&p

# read chars from fd 4 (jail stdout), print to stdout
while IFS= read -u4 -r -k1 char; do
    print -u1 -n -- "$char"
done &
# read chars from stdin, send to jail stdin if valid
while IFS= read -u0 -r -k1 char; do
    if [[ ! ${#char:|charset} -eq 0 ]]; then
        print -P "\n%F{red}Nope.%f\n"
        exit 1
    fi
    # send to fd 3 (jail stdin)
    print -u3 -n -- "$char"
done
```

#### Step one: Find the executable
The first order of business is to find out where the flag is, which is easy enough.  
We can pick a charset of `Rls/- ` to execute the command: `ls -R /` on the server  
Lets just save the output to a file with: `nc target port > file.txt 2>&1`  
After saving it to a file we can `grep` the file for the `getflag` binary which reveals the location:  
`/app/y0u/w1ll/n3v3r_g3t/th1s/getflag`

#### Step two: Running the executable
Since it will take way more than just 6 distinct characters to type that path and file name, we need a way of substituting characters  
This is where character matching comes in. In zshell you can put characters in brackets to match them. So `[ab]` would mean a or b, and `[^0-9]` would mean any non number.  
Equiped with this knowledge we can just use matching syntax to match each character with something like `[^z]`
Which will form a full payload:
```zsh
/[^z][^z][^z]/[^z][^z][^z]/[^z][^z][^z][^z]/[^z][^z][^z][^z][^z][^z][^z][^z][^z]/[^z][^z][^z][^z]/[^z][^z][^z][^z][^z][^z][^z]
```

Which runs the getflag binary and reveals the flag: `dice{d0nt_u_jU5T_l00oo0ve_c0d3_g0lf?}`
