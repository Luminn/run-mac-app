#!/usr/bin/env Python
import os
import sys
import re

#split file name based on common name separators on macOS
def super_split(string):
    return re.split("-|\.| |_|#", string)

#fix common path to the syntax (\ ) used in argument list
def fix_open_path(p):
    s = ""
    for i in p:
        if i != " ":
            s += i
        else:
            s += "\\ "
    return s

#combine list of strings with spaces as a separators
def comb_names(args):
    s = ""
    for st in args:
        s += st + " "
    return s[:-1]

#run the program using command "open"
open_args = None
def run(p):
    if open_args == None:
        os.system("open " + fix_open_path(p))
    else:
        os.system("open " + fix_open_path(p) + " --args " + comb_names(open_args))

#get the acronym of the filename
def get_acro_str(name):
    res = ""
    sep = super_split(name)
    for s in sep:
        if len(s) != 0 and str.isalpha(s[0]):
            res += s[0]
    return res

ls_flag = False         #if true, list all app's name
if len(sys.argv) == 1:
    print("run [filename args]")
    print("please specify arguments")
    sys.exit(0)
args = sys.argv[1:]
if args[0] == "--version":
    print("macrun v1.0.")
    sys.exit(0)
elif args[0] == "--help":
    print("run [file name args]")
    print("This program will run a program with a identical name.")
    print("The program must be located in the application folder.")
    sys.exit(0)
elif args[0] == "--list":
    print("Listing ALL your applications:")
    ls_flag = True

#clear the .app postfix and find "--args"
has_arguments = False
for i in range(len(args)):
    if args[i] == "--args":
        has_arguments = True
        break
    if args[i].endswith(".app"):
        args[i] = args[i][:-4]

#separate file name args with open args
if has_arguments:
    arg_i = args.index("--args")
    open_args = args[arg_i + 1:]
    args = args[:arg_i]
    if len(args) == 0:
        print("Please specify some file arguments.")
        sys.exit(0)

app_dir = "/Applications/"
app_files = os.listdir(app_dir)
prime_applications = []     #all application directly in the Applications folder
lp_applications = []        #applications in a sub folder

#get all application's data
for f in app_files:
    f_path = os.path.join(app_dir,f)
    if f.endswith(".app") and os.access(f_path, os.X_OK):
        prime_applications.append((f[:-4],f_path))
    elif (not f.startswith(".")) and os.path.isdir(f_path):
        for child in os.listdir(f_path + os.sep):
            c_path = os.path.join(f_path, child)
            if child.endswith(".app") and os.access(c_path, os.X_OK):
                lp_applications.append((child[:-4], c_path))

#do the listing
if ls_flag:
    big_list = prime_applications + lp_applications
    big_list.sort()
    for f in big_list:
        print (f[0])
    sys.exit(0)


# Phase One: Perfect Fit Test
comb_name = comb_names(args)
for name in prime_applications:
    if name[0] == comb_name:
        run(name[1])
        sys.exit(0)
for name in lp_applications:
    if name[0] == comb_name:
        run(name[1])
        sys.exit(0)

# Phase Two: Case Insensitive Test
for name in prime_applications:
    if name[0].lower() == comb_name.lower():
        run(name[1])
        sys.exit(0)
for name in lp_applications:
    if name[0].lower() == comb_name.lower():
        run(name[1])
        sys.exit(0)

# Phase Three: Acronym Test, Ex: Multi Media Player.app matches mmp
if len(args) == 1 and len(args[0]) > 1:
    for name in prime_applications:
        if args[0].lower() == get_acro_str(name[0]).lower():
            run(name[1])
            sys.exit(0)
    for name in lp_applications:
        if args[0].lower() == get_acro_str(name[0]).lower():
            run(name[1])
            sys.exit(0)


# Phase Four: Matching Test, Ex: Multi Media Player.app is a match to multi player but not mult player
match_fit = []
for name in prime_applications:
    components = super_split(name[0])
    is_match = True
    for arg in args:
        mat = False
        for comp in components:
            if arg.lower() == comp.lower():
                mat = True
        if mat == False:
            is_match = False
            break
    if is_match:
        match_fit.append(name[1])
if len(match_fit) == 1:
    run(match_fit[0])
    sys.exit(0)
elif len(match_fit) > 1:
    print("Did you mean:")
    for name in match_fit:
        print(os.path.basename(name))
    sys.exit(0)

# Check High Priority Applications first
for name in lp_applications:
    components = super_split(name[0])
    is_match = True
    for arg in args:
        mat = False
        for comp in components:
            if arg.lower() == comp.lower():
                mat = True
        if mat == False:
            is_match = False
            break
    if is_match:
        match_fit.append(name[1])
if len(match_fit) == 1:
    run(match_fit[0])
    sys.exit(0)
elif len(match_fit) > 1:
    print("Did you mean:")
    for name in match_fit:
        print(os.path.basename(name))
    sys.exit(0)

# Phase 5 Regex Text
if len(arg) == 1 and len(arg[0]) == 1:
    print("No Match!")
    sys.exit(0)
try:
    re_list = []
    for name in prime_applications:
        re_matched = True
        for arg in args:
            if not bool(re.match(".*"+arg.lower(), name[0].lower())):
                re_matched = False
                break
        if re_matched:
            re_list.append(name[1])
    if len(re_list) == 1:
        run(re_list[0])
        sys.exit(0)
    elif len(re_list) > 1:
        print("Did you mean:")
        for name in re_list:
            print(os.path.basename(name))
        sys.exit(0)

    re_list = []
    for name in lp_applications:
        re_matched = True
        for arg in args:
            if not bool(re.match(".*"+arg.lower(), name[0].lower())):
                re_matched = False
                break
        if re_matched:
            re_list.append(name[1])
    if len(re_list) == 1:
        run(re_list[0])
        sys.exit(0)
    elif len(re_list) > 1:
        print("Did you mean:")
        for name in re_list:
            print(os.path.basename(name))
        sys.exit(0)
    else:
        print("No Match!")
        sys.exit(0)
except re.error:
    print("No Match!")
    sys.exit(0)
