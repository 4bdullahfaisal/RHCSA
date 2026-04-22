# Module 2 Essentials Tools
---
**VM:** Oracle Linux 9 on VirtualBox
---
## Linux Commands & Practice

**head** shows the first lines of a file. `head /etc/passwd` shows the first 10 lines. `head -n 5 /etc/passwd` shows only the first 5 lines.

**tail** shows the last lines of a file. `tail /etc/passwd` shows the last 10 lines. `tail -n 3 /etc/passwd` shows the last 3 lines. `tail -f /var/log/messages` follows the log in real time — press Ctrl+C to stop.

**passwd** changes your password. Just type `passwd` and follow the prompts. `sudo passwd username` changes another user's password.

**man** opens the manual page for any command. `man ls`, `man head`, `man tail`, `man grep`. Press `q` to exit.

**nano** is a terminal text editor. `nano win.txt` opens or creates a file. Inside nano: Ctrl+O saves, Ctrl+X exits, Ctrl+W searches, Ctrl+K cuts a line, Ctrl+U pastes.

---

**Hard links** make the same file have two names. They share the same inode number. Delete the original and the link still works.

Create a file: `echo "sample text" > win.txt`

Create a hard link: `ln win.txt win_link`

Check they are the same: `ls -li win.txt win_link` — both show the same inode number.

Delete the original: `rm win.txt`

The link still works: `cat win_link`

---

**Soft links** (symbolic links) are shortcuts. They have a different inode and break if the original is deleted.

Create a soft link: `ln -s win.txt win-soft.txt`

Check it: `ls -li win.txt win-soft.txt` — you'll see `win-soft.txt -> win.txt`

Delete the original: `rm win.txt`

The link breaks: `cat win-soft.txt` gives "No such file or directory"

---

**File globbing** uses wildcards to match multiple files.

`*` matches any characters. `ls *.txt` shows all text files. `ls file*` shows all files starting with "file".

`?` matches exactly one character. `ls file?.txt` matches file1.txt, file2.txt, fileA.txt but not file10.txt.

`[abc]` matches one character from the set. `ls file[123].txt` matches file1.txt, file2.txt, file3.txt.

`[a-z]` matches one character in a range. `ls file[a-c].txt` matches filea.txt, fileb.txt, filec.txt.

`[!abc]` matches one character NOT in the set. `ls file[!0-9].txt` matches files that don't end with a number.

---

**grep** searches inside files for patterns.

Basic grep: `grep "fire" data.txt` finds lines with "fire". `grep -i "the" data.txt` ignores case. `grep -n "is" data.txt` shows line numbers. `grep -v "is" data.txt` shows lines NOT matching. `grep -c "the" data.txt` counts matches.

Character sets in grep: `grep "fi[a-zA-Z]e" data.txt` matches fi + any letter + e (file, fine, fire). `grep "fi[a-r]e" data.txt` matches fi + letters a through r + e. `grep "fi[a-m]e" data.txt` matches only fi + a through m + e (file but not fine or fire). `grep "fi[^0-9]e" data.txt` matches fi + anything except a digit + e.

Line start and end: `grep "^the" data.txt` finds lines starting with "the". `grep -i "^the" data.txt` finds lines starting with "the" or "The". `grep "[0-9]$" data.txt` finds lines ending with a digit. `grep "e$" data.txt` finds lines ending with the letter e.

---

**Extended grep** (`grep -E`) adds more powerful patterns.

`+` means one or more of the previous character. `grep -E "fil+e" data.txt` matches file, fille, fillle (fi + one or more l + e).

`?` means zero or one of the previous character. `grep -E "fil?e" data.txt` matches fie (zero l) and file (one l).

`{}` specifies exact counts. `grep -E "fil{2,4}e" data.txt` matches fille (2 l's), fillle (3 l's), filllle (4 l's).

`|` means OR. `()` groups patterns. `grep -E "fi(ne|le|re)" data.txt` matches fine, file, fire. `grep -E "fi(ne|le)" data.txt` matches fine and file but not fire.
