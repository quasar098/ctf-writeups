# markov mystery

## problem

My friend Markov says he lost the main system, but I know nothing of these weird stuff.

see `output.txt`

see `markovgraphy.pyc`

## solution

pyc files are compiled python files, and are not human readable

what we can do to decrypt it is use pycdc

the problem here is that some of the opcodes are not found and so pycdc fails

what we can do is just clone a copy from [here](https://github.com/zrax/pycdc) and then remove the error message on the missing opcode

```diff
diff --git a/ASTree.cpp b/ASTree.cpp
index e7c9262..0fb62ee 100644
--- a/ASTree.cpp
+++ b/ASTree.cpp
@@ -2363,10 +2363,6 @@ PycRef<ASTNode> BuildFromCode(PycRef<PycCode> code, PycModule* mod)
         case Pyc::PUSH_NULL:
             stack.push(nullptr);
             break;
-        default:
-            fprintf(stderr, "Unsupported opcode: %s\n", Pyc::OpcodeName(opcode & 0xFF));
-            cleanBuild = false;
-            return new ASTNodeList(defblock->nodes());
         }

         else_pop =  ( (curblock->blktype() == ASTBlock::BLK_ELSE)
```

then we can compile it and use it to get the following:

```py
# Source Generated with Decompyle++
# File: markovgraphy.pyc (Python 3.10)

import random
import numpy as np

with open('flag.txt', 'r') as f:
    FLAG = f.read()

random_letter_number = 'abcdefghijklmnopqrstuvwxyz0123456789'
system_1_states = np.array([
    't',
    random.choice(random_letter_number),
    random.choice(random_letter_number)])
system_1 = np.array([
    [
        0.125,
        0.428571,
        0.75],
    [
        0.75,
        0.142857,
        0.25],
    [
        0.125,
        0.428571,
        0]])


def create_system(letter):
    size = random.randint(5, 10)
    states = []
    for i in range(size):
        random_letter = random.choice(random_letter_number)
        if random_letter not in states and random_letter != letter:
            pass

        states.append(random_letter)

    states = np.array(states)
    states[random.randint(0, size - 1)] = letter
    transition_matrix = np.random.rand(size, size)
    transition_matrix = transition_matrix / transition_matrix.sum(axis=1, keepdims=True)
    values = the_markov_mystery(transition_matrix)

    dictionary = {}
    for i in range(size):
        dictionary[states[i]] = values[i]

    highest = max(dictionary, key=dictionary.get)
    dictionary[letter] = dictionary[highest]
    dictionary[highest] = dictionary[letter]

    # swap heighest and j
    for i in range(size):
        if states[i] == highest:
            dummy = i
            states[i] = letter
    for j in range(size):
        if states[j] == letter and j != dummy:
            states[j] = highest

    return states, dictionary, transition_matrix


def the_markov_mystery(p):
    # deterministic btw
    dim = p.shape[0]
    q = p - np.eye(dim)
    ones = np.ones(dim)
    q = np.c_[(q, ones)]
    QTQ = np.dot(q, q.T)
    bQT = np.ones(dim)
    return np.linalg.solve(QTQ, bQT)


if __name__ == '__main__':
    flag_dictionary = {}
    i = 1
    for letter in FLAG:
        states, dictionary, transition_matrix = create_system(letter)
        transition_dictionary = {}
        for k_index in range(len(states)):
            transition_dictionary[states[k_index]] = transition_matrix[k_index]
        flag_dictionary[i] = transition_dictionary
        print('System ' + str(i) + ' \n')
        print('States: ' + str(states) + '\n')
        print('Transition matrix: \n' + str(transition_matrix) + '\n')
        print('Dictionary: \n' + str(transition_dictionary) + '\n')
        i += 1
```

we can see that the largest value of the output of the_markov_mystery is swapped with the actual letter of the flag

from this, we make a solve script

```py
import numpy as np


with open("out.txt") as f:
    systems = []
    states = []
    save_data = ""
    next_is_transition_matrix = False
    for line in f.read().splitlines(False):
        line = line.replace("\x00", "")
        if line.startswith("States:"):
            states = [_[1] for _ in line[9:-1].split(" ")]
        if line.startswith("Tran"):
            next_is_transition_matrix = True
            continue
        if next_is_transition_matrix:
            if line.startswith("Dic"):
                systems.append([eval(save_data), states])
                states = []
                save_data = ""
                next_is_transition_matrix = False
                continue
            save_data += line.replace("  ", " ").replace("  ", " ").replace("  ", " ").replace(" ", ",")


def the_markov_mystery(p):
    # deterministic
    dim = p.shape[0]
    q = p - np.eye(dim)
    ones = np.ones(dim)
    q = np.c_[(q, ones)]
    QTQ = np.dot(q, q.T)
    bQT = np.ones(dim)
    return np.linalg.solve(QTQ, bQT)


for system in systems:
    transition_matrix, states2 = system
    data2 = the_markov_mystery(np.array(transition_matrix)).tolist()
    data2 = [float(_) for _ in data2]
    print(states2[data2.index(max(data2))], end="")

```

flag is `ictf{0h_d4mn_m4rk0v_ch41n5_4r3nt_th4t_b4d}`

