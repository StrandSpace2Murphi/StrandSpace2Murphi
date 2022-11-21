StrandSpace2Murphi
====
StrandSpace2Murphi is an automatic translator to bridge the gap between high-level Strand Space specifications and low-level Murphi model checker, which can help verify the security protocol described in the Strand Space specifications.<br>

Difficulty<br>
---
- There are three agents in Otway-Rees including Alice, Bob and Server. Besides, We implemented a mechanism for forwarding secrecy with the help of `tmp` message. When agent receives oneA message that cannot be decrypted from its own knowledge, it forwards this message tagged as `tmp` to another agent.
- Diffie Hellman is one of the earliest practical examples of public key exchange implemented within the field of cryptography which involes sophicated mathematics theories, e.g., modular exponentiation. The intruder supports the man-in-the-middle attack, which plays the role of sending the forged message to Alice and Bob respectively. Thus, Alice and Bob  thought  he was communicating with the intended agents.
- 5G EAP-TLS authentication is a practical protocol to ensure the communication security. There are four participating entities in this protocol, UE, SEAF, AUSF and SUPI. It's hard to explore the whole state space because of numerous message exchanged. Besides, EAP-TLS uses a negotiated key  as the encrypted key which is a tetrad hash message.

Theory protocol for paper<br>
---
*A Formally Verified Scheme for SecurityProtocols with the Operational Semantics ofStrand Space*<br>
>The main security protocols verified:<br>
<table>
    <tr>
        <th>Protocols</th><th>Unsatisfied</th><th>Time(sec.)</th><th>Memory(byte.)</th>
    </tr>
    <tr>
        <td rowspan="2">Needham-Schroder public key</td><td>secrecy(Nb)</td><td>0.10</td><td rowspan="2">565</td>
    </tr>
    <tr>
        <td>weakB</td><td>0.15</td>
    </tr>
    <tr>
        <td>Lowe's fixed Needham-Schroder public key</td><td>no errors</td><td>0.10</td><td>584</td>
    </tr>
    <tr>
        <td>Diffie-Hellman key exchange</td><td>secrecy(Na)</td><td>0.10</td><td>790</td>
    </tr>
     <tr>
        <td rowspan="3">CCITT X.509(1)</td><td>secrecy(Ya)</td><td>0.21</td><td rowspan="3">660</td>
    </tr>
    <tr>
        <td>weakB</td><td>0.84</td>
    </tr>
    <tr>
        <td>weakA</td><td>0.84</td>
    </tr>
    <tr>
        <td>CCITT X.509(1c)</td><td>no errors</td><td>0.45</td><td>698</td>
    </tr>
    <tr>
        <td>Woo and Lam Pi</td><td>secrecy(Nb)</td><td>0.10</td><td>776</td>
    </tr>
    <tr>
        <td>Andrew Secure RP</td><td>secrecy(Kab)</td><td>2.77</td><td>768</td>
    </tr>
     <tr>
        <td rowspan="2">EAP-TLS authentication</td><td>secrecy(prekey)</td><td>1.21</td><td rowspan="2">3018 </td>
    </tr>
    <tr>
        <td>weakC</td><td>51.55</td>
    </tr>
     <tr>
        <td>EAP-AKA authentication</td><td>secrecy(snn)</td><td>1.50</td><td>1388</td>
    </tr>
</table>
 

Transition Scheme
---
*The Transformation from the Operational Semantic to Transition Rules*<br>


>>|Strand Rules | Murphi Guard | Murphi Action|
>>|:---:|:---: | :---:|
>>|Send(i,str,s,M,L)  | c.empty | Send(M); i := i+1; L.add(M)|
>>|Recv(i,str,s,M,L) | !c.empty |  Recv(M); i := i+1; L.remove(M); msg := destruct(M); update(msg)|
>>|Emit(i,str,s,M,L)  | c.empty & SpyK(M) |  Emit(M); L.add(M)|
>>|Flush(i,str,s,M,L) | !c.empty & !SpyK(M) |  Flush(M); L.remove(M); SpyK(M) := True|
>>|Sep(i,str,s,M,L)|  M = Mpair(m1,m2) & SpyK(M) | Sep(M); SpyK(m1) := True; SpyK(m2) := True|
>>|Cat(i,str,s,M,L) | SpyK(m1) & SpyK(m2) & !SpyK(Mpair(m1,m2)) |  Cat(m1,m2); SpyK(Mpair(m1,m2)) := True|
>>|Dec(i,str,s,M,L)|  SpyK(M) & type(M) = Crypt(m,k) & SpyKnow(k) |  Dec(M,k); SpyK(m) := True|
>>|Enc(i,str,s,M,L)|  SpyK(m) & SpyK(k) & !SpyK(Crypt(m,k)) |  Enc(m,k); SpyK(Crypt(m,k)) := True|
>>|Mod(i,str,s,M,L)|  SpyK(m1) & SpyK(m2) & !SpyK(Mod(m,k)) |  Mod(m1,m2); SpyK(Mod(m1,m2)) := True|
>>|Exp(i,str,s,M,L)|  SpyK(m1) & SpyK(m2) & !SpyK(Exp(m,k)) |  Mod(m1,m2); SpyK(Exp(m1,m2)) := True|


Installation<br>
---
In our experiment, StrandSpace2Murphi tool is run on a PC server with macOS Catalina.<br>

Install StrandSpace2Murphi Environment<br>

StrandSpace2Murphi uses Ocaml 4.04.0, Murphi 5.4.9.1 and requires several ocaml libraries to run, which contains:<br>
- Menhir
- Murphi 
- Core


Run with StrandSpace2Murphi<br>
---
To use StrandSpace2Murphi, you need to comfirm the your computer equipped with  Ocaml 4.04.0, Murphi 5.4.9.1, Core, and Menhir in your environment.<br>

Running the following command in terminal to verify the protocol models. In this example, we verify the Needham-Schoreder public key protocol model `NSPK.txt`.

First, execute the following command to use StrandSpace2Murphi to compile the A&B specification.

$ corebuild getModelString.byte -use-menhir 

Then, StrandSpace2Murphi will generate the `getModelString.byte` which can help compile the source protocol into Murphi code.

$ ./getModelString.byte ./protocol/NSPK.txt

Next, the protocol is compiled into Murphi code, the output file is located in `/source-code/outputs/result.m`.

$ cd outputs 

$ `cmurphi-path`/cmurphi5.4.9.1/src/mu result.m

$ g++ -o result.o result.cpp -I `cmurphi-path`/cmurphi5.4.9.1/include/ -ggdb

$ ./result.o >out1 -ndl -tv

Over verification:

Result: `Invariant "weakB" failed`.

State Space Explored: `79 states, 110 rules fired in 0.10s`.

This hint indicates that StrandSpace2Murphi uses 79 states and 110 rules to find the counterexample path of invariant `weakB`.

