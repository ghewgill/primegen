import os
import shutil
import sys

env = Environment()

def choose(env, target, source):
    choices = [x.path for x in source[:2]]
    trial = source[2].path
    r = os.system(trial)
    src = choices[0] if r == 0 else choices[1]
    shutil.copyfile(src, target[0].path)

conf = Configure(env)

tryulong32 = env.Program("tryulong32.c")
trylong64 = env.Program("trylong64.c")
tryulong64 = env.Program("tryulong64.c")

env.Command("uint32.h", ["uint32.h2", "uint32.h1", tryulong32], choose)
env.Command("int64.h", ["int64.h1", "int64.h2", trylong64], choose)
env.Command("uint64.h", ["uint64.h1", "uint64.h2", tryulong64], choose)
open("hasrdtsc.h", "w").write("#define HASRDTSC 1\n" if conf.TryRun(open("tryrdtsc.c").read(), ".c")[0] else "")
open("hasgethr.h", "w").write("#define HASGETHRTIME 1\n" if conf.TryLink(open("trygethr.c").read(), ".c") else "")

env.Command("primegen.h", ["primegen.h.sed", "conf-words"], lambda env, target, source: open(target[0].path, "w").write(open(source[0].path).read().replace("conf-words", open(source[1].path).readline())))

primegen = env.Library("primegen", [
    "primegen.c",
    "primegen_init.c",
    "primegen_next.c",
    "primegen_skip.c",
])

env.Program("primes", ["primes.c", "fs64.c", primegen])
env.Program("primegaps", ["primegaps.c", "fs64.c", primegen])
if os.name == "posix":
    env.Program("primespeed", ["primespeed.c", "fs64.c", primegen])
    env.Program("eratspeed", ["eratspeed.c"])
