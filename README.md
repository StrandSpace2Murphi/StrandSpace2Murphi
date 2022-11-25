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
*A Formally Verified Scheme for SecurityProtocols with the Operational Semantics of Strand Space*<br>
>The main security protocols verified:<br>
<table>
    <tr>
        <th>Protocols</th><th>Unsatisfied</th><th>Time(sec.)</th><th>Memory(byte)</th>
    </tr>
    <tr>
        <td rowspan="2" align="center">Needham-Schroder public key</td><td align="center">secrecy(Nb)</td><td align="center">0.10</td><td rowspan="2" align="center">565</td>
    </tr>
    <tr>
        <td align="center">weakB</td><td align="center">0.15</td>
    </tr>
    <tr>
        <td align="center">Lowe's fixed Needham-Schroder public key</td><td align="center">no errors</td><td align="center">0.10</td><td align="center">584</td>
    </tr>
    <tr>
        <td align="center">Diffie-Hellman key exchange</td><td align="center">secrecy(Na)</td><td align="center">0.10</td><td align="center">790</td>
    </tr>
     <tr>
        <td rowspan="3" align="center">CCITT X.509(1)</td><td align="center">secrecy(Ya)</td><td align="center">0.21</td><td rowspan="3" align="center">660</td>
    </tr>
    <tr>
        <td align="center">weakB</td><td align="center">0.84</td>
    </tr>
    <tr>
        <td align="center">weakA</td><td align="center">0.84</td>
    </tr>
    <tr>
        <td align="center">CCITT X.509(1c)</td><td align="center">no errors</td><td align="center">0.45</td><td align="center">698</td>
    </tr>
    <tr>
        <td align="center">Woo and Lam Pi</td><td align="center">secrecy(Nb)</td><td align="center">0.10</td><td align="center">776</td>
    </tr>
    <tr>
        <td align="center">Andrew Secure RP</td><td align="center">secrecy(Kab)</td><td align="center">2.77</td><td align="center">768</td>
    </tr>
     <tr>
        <td rowspan="2" align="center">EAP-TLS authentication</td><td align="center">secrecy(prekey)</td><td align="center">1.21</td><td rowspan="2" align="center">3018 </td>
    </tr>
    <tr>
        <td align="center">weakC</td><td align="center">51.55</td>
    </tr>
     <tr>
        <td align="center">EAP-AKA authentication</td><td align="center">secrecy(snn)</td><td align="center">1.50</td><td align="center">1388</td>
    </tr>
</table>
 


Transition Scheme
---
>The Transformation from the Operational Semantic to Transition Rules<br>

 <table>
    <tr>
        <th>Strand Rule</th><th>Murphi Guard</th><th>Murphi Action</th>
    </tr>
    <tr>
        <td align="center">Send(i,str,M,L)</td>
        <td>c.empty</td>
        <td>Send(M); i := i + 1; L.add(M)</td>
    </tr>
    <tr>
        <td  align="center">Recv(i,str,M,L)</td>
        <td>!c.empty</td>
        <td>Recv(M); i := i + 1;L.remove(M);<br>msg := destruct; update(msg)</td>
    </tr>
    <tr>
        <td align="center">Emit(i,str,M,L)</td>
        <td>c.empty & SpyK(M)</td>
        <td>Emit(M); L.add(M)</td>
    </tr>
    <tr>
        <td align="center">Flush(i,str,M,L)</td>
        <td>!c.empty & !SpyK(M)</td>
        <td>Flush(M); L.remove(M); SpyK(M) := True</td>
    </tr>
    <tr>
        <td align="center">Sep(i,str,s,M,L)</td>
        <td>M = Mpair(m1,m2) & SpyK(M)</td>
        <td>Sep(M); SpyK(m1) := True; SpyK(m2) := True</td>
    </tr>
    <tr>
        <td align="center">Cat(i,str,s,M,L)</td>
        <td>SpyK(m1) & SpyK(m2) & !SpyK(Mpair(m1,m2))</td>
        <td>Cat(m1,m2); SpyK(Mpair(m1,m2)) := True</td>
    </tr>
    <tr>
        <td align="center">Dec(i,str,s,M,L)</td>
        <td>SpyK(M) & type(M)=Crypt(m,k) & SpyKnow(k)</td>
        <td>Dec(M,k); SpyK(m) := True</td>
    </tr>
    <tr>
        <td align="center">Enc(i,str,s,M,L)</td>
        <td>SpyK(m) & SpyK(k) & !SpyK(Crypt(m,k))</td>
        <td>Enc(m,k); SpyK(Crypt(m,k)) := True</td>
    </tr>
    <tr>
        <td align="center">Mod(i,str,s,M,L)</td>
        <td>SpyK(m1) & SpyK(m2) & !SpyK(Mod(m,k))</td>
        <td>Mod(m1,m2); SpyK(Mod(m1,m2)) := True </td>
    </tr>
    <tr>
        <td align="center">Exp(i,str,s,M,L)</td>
        <td>!c.empty & !SpyK(M)</td>
        <td>Mod(m1,m2); SpyK(Exp(m1,m2)) := True</td>
    </tr>
    
</table>


Installation<br>
---
In our experiment, StrandSpace2Murphi tool is run on a PC server with macOS Catalina.<br>

Install StrandSpace2Murphi Environment<br>

StrandSpace2Murphi uses Ocaml 4.04.0, Murphi 5.4.9.1 and requires several ocaml libraries to run, which contains:<br>
- Menhir
- Core
- Yacc


Run with StrandSpace2Murphi<br>
---
To use StrandSpace2Murphi, you need to comfirm the your computer equipped with  Ocaml 4.04.0, Murphi 5.4.9.1, Core, and Menhir in your environment.<br>

#### Running the following command in terminal to verify the protocol models. In this example, we verify the Needham-Schoreder public key protocol model `NSPK.txt`.

1. Execute the following command to use StrandSpace2Murphi to compile Strand Space specification.
    - $ corebuild getModelString.byte -use-menhir 

2. StrandSpace2Murphi will generate the `getModelString.byte` which can help compile the security protocol into Murphi code.
    - $ ./getModelString.byte ./protocol/NSPK.txt

3. The generated file is located in `/source-code/outputs/result.m` after the protocol is compiled into Murphi code.
    - $ cd outputs 
    - $ `cmurphi-path`/cmurphi5.4.9.1/src/mu result.m
    - $ g++ -o result.o result.cpp -I `cmurphi-path`/cmurphi5.4.9.1/include/ -ggdb

4. Execute the verification of the generated murphi script:
    - $ ./result.o >out1 -ndl -tv

#### Result: `Invariant "weakB" failed`.

#### State Space Explored: `79 states, 110 rules fired in 0.10s`.

This hint indicates that StrandSpace2Murphi uses 79 states and 110 rules to find the counterexample path of invariant `weakB`.

Case Study<br>
---
>EAP-TLS Protocol<br>
![ ]([http://static.runoob.com/images/runoob-logo.png](https://github.com/StrandSpace2Murphi/StrandSpace2Murphi/blob/main/image/EAP-TLS.png))



