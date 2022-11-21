StrandSpace2Murphi
====
StrandSpace2Murphi is an automatic translator to bridge the gap between high-level Strand Space specifications and low-level Murphi model checker, which can help verify the security protocol described in the Strand Space specifications.<br>


Theory protocol for paper<br>
---
*A Formally Verified Scheme for SecurityProtocols with the Operational Semantics ofStrand Space*<br>
>The main security protocols proved are:<br>
>>|Protocols | Unsatisfied  | Time (sec.) | Memory|
>>|:---:|:---:|:---:|:---:|
>>|Needham-Schroder public key| secrecy(Nb)|0.10|56|
>>||weakB|0.15|56|
>>|Lowe's fixed Needham-Schroder public key| no error|0.10|56|
>>|Diffie-Hellman key exchange|secrecy(Na)|0.10|64|
>>|Otway-Rees |no error|2.13|177| 
>>|CCITT X.509(1)|secrecy(Ya)|0.21|53| 
>>|CCITT X.509(1)|weakB|0.84|53| 
>>|CCITT X.509(1)|weakA|0.84|53| 
>>|CCITT X.509(1c)|no error|0.45|53| 
>>|Woo and Lam Pi|secrecy(Nb)|0.10|69| 
>>|Andrew Secure RP|secrecy(Kab)|2.77|54| 
>>|EAP-TLS authentication|secrecy(prekey)|1.21|1700| 
>>|EAP-TLS authentication|weakC|51.55|1700| 
>>|EAP-AKA authentication|secrecy(snn)|1.50|1700| 

<table>
    <tr>
        <th>Protocols</th><th>Unsatisfied</th><th>Time(sec.)</th><th>Memory(kb.)</th>
    </tr>
    <tr>
        <td rowspan="2">Needham-Schroder public key</td><td>secrecy(Nb)</td><td>0.10</td><td>56</td>
    </tr>
    <tr>
        <td>weakB</td><td>0.15</td><td>56</td>
    </tr>
    <tr>
        <td>Lowe's fixed Needham-Schroder public key</td><td>no errors</td><td>0.10</td><td>56</td>
    </tr>
    <tr>
        <td>Diffie-Hellman key exchange</td><td>secrecy(Na)</td><td>0.10</td><td>64</td>
    </tr>
     <tr>
        <td rowspan="3">CCITT X.509(1)</td><td>secrecy(Ya)</td><td>0.21</td><td>53</td>
    </tr>
    <tr>
        <td>weakB</td><td>0.84</td><td>53</td>
    </tr>
    <tr>
        <td>weakA</td><td>0.84</td><td>53</td>
    </tr>
    <tr>
        <td>CCITT X.509(1c)</td><td>no errors</td><td>0.45</td><td>53</td>
    </tr>
    <tr>
        <td>Woo and Lam Pi</td><td>secrecy(Nb)</td><td>0.1</td><td>69</td>
    </tr>
    <tr>
        <td>Andrew Secure RP</td><td>secrecy(Kab)</td><td>2.77</td><td>54</td>
    </tr>
     <tr>
        <td rowspan="2">EAP-TLS authentication</td><td>secrecy(prekey)</td><td>1.21</td><td>1700</td>
    </tr>
    <tr>
        <td>weakC</td><td>51.55</td><td>1700</td>
    </tr>
     <tr>
        <td>EAP-AKA authentication</td><td>secrecy(snn)</td><td>1.50</td><td>1700</td>
    </tr>
</table>
 

Transition Scheme
---
*The Transformation from the Operational Semantic to Transition Rules*<br>
>>|Rule | Murphi Rule|
>>|:---:|:---|
>>|Send(i, str, s, M, L) | rule "send" channel.empty ==> Send(M); i := i+1; L.add(M);|
>>|Recv(i, str, s, M, L) | rule "recv" !channel.empty ==> Recv(M); i := i+1; L.remove(M); msg := destruct(M); update(msg);|
>>|Emit(i, str, s, M, L) | rule "emit" channel.empty & SpyKnow(M) ==> Emit(M); L.add(M)|
>>|Flush(i, str, s, M, L)| rule "flush" !channel.empty & !SpyKnow(M) ==> Flush(M); L.remove(M); SpyKnown(M) := True;|
>>|Sep(i, str, s, M, L)|rule "sep" type(M) = Mpair(m1,m2) & SpyKnow(M) ==> Sep(M); SpyKnow(m1) := True; SpyKnow(m2) := True;|
>>|Cat(i, str, s, M, L)| rule "cat" SpyKnow(m1) & SpyKnow(m2) & !SpyKnow(Mpair(m1,m2)) ==> Cat(m1,m2); SpyKnow(Mpair(m1,m2)) := True;|
>>|Dec(i, str, s, M, L)| rule "dec" SpyKnow(M) & type(M) = Crypt(m,k) & SpyKnow(k) ==> Dec(M,k); SpyKnow(m) := True;|
>>|Enc(i, str, s, M, L)|rule "enc" SpyKnow(m) & SpyKnow(k) & !SpyKnow(Crypt(m,k)) ==> Enc(m,k); SpyKnow(Crypt(m,k)) := True;|
>>|Mod(i, str, s, M, L)|rule "mod" SpyKnow(m1) & SpyKnow(m2) & !SpyKnow(Mod(m,k)) ==> Mod(m1,m2); SpyKnow(Mod(m1,m2)) := True;|
>>|Exp(i, str, s, M, L)|rule "exp" SpyKnow(m1) & SpyKnow(m2) & !SpyKnow(Exp(m,k)) ==> Mod(m1,m2); SpyKnow(Exp(m1,m2)) := True;|


Difficulty<br>
---
- There are three agents in Otway-Rees including Alice, Bob and Server. Besides, We implemented a mechanism for forwarding secrecy with the help of `tmp` message. When agent receives oneA message that cannot be decrypted from its own knowledge, it forwards this message tagged as `tmp` to another agent.
- Diffie Hellman is one of the earliest practical examples of public key exchange implemented within the field of cryptography which involes sophicated mathematics theories, e.g., modular exponentiation. The intruder supports the man-in-the-middle attack, which plays the role of sending the forged message to Alice and Bob respectively. Thus, Alice and Bob  thought  he was communicating with the intended agents.
- 5G EAP-TLS authentication is a practical protocol to ensure the communication security. There are four participating entities in this protocol, UE, SEAF, AUSF and SUPI. It's hard to explore the whole state space because of numerous message exchanged. Besides, EAP-TLS uses a negotiated key  as the encrypted key which is a tetrad hash message.

Installation<br>
---
In our experiment, StrandSpace2Murphi tool is run on a PC server with macOS Catalina.<br>

Install StrandSpace2Murphi Environment<br>

StrandSpace2Murphi uses Ocaml 4.04.0, Murphi 5.4.9.1 and requires several ocaml libraries to run, which contains:<br>
- Menhir
- Murphi 
- Core


Usage
---
To use StrandSpace2Murphi, you need to comfirm the your computer equipped with  Ocaml 4.04.0, Murphi 5.4.9.1, Core, and Menhir in your environment.<br>

Running the following command in terminal to verify the protocol models. In this example, we verify the Needham-Schoreder public key protocol model `NSPK.txt`.

First, execute the following command to use StrandSpace2Murphi to compile the A&B specification.

$ corebuild getModelString.byte -use-menhir 

Then, StrandSpace2Murphi will generate the `getModelString.byte` which can help compile the source protocol into Murphi code.

$ ./getModelString.byte ./protocol/NSPK.txt

Next, the protocol is compiled into Murphi code, the output file is located in `/source-code/outputs/result.m`.

$cd outputs 

$ `cmurphi-path`/cmurphi5.4.9.1/src/mu result.m

$ g++ -o result.o result.cpp -I `cmurphi-path`/cmurphi5.4.9.1/include/ -ggdb

$ ./result.o >out1 -ndl -tv

over verification, Result: `Invariant "weakB" failed.`. State Space Explored: `79 states, 110 rules fired in 0.10s.`

This message indicates StrandSpace2Murphi uses 79 states and 110 rules to find the counterexample path of invariant `weakB`.

