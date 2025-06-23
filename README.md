# Secure Vault Access System

This design implements a 5-state D-flip-flop FSM that requires **two correct code entries** followed by **one biometric check** before granting access. Any wrong code or biometric immediately drives the FSM into the **Incorrect** state, from which the next press of the **ENTER** button (also used as reset) returns it to **Idle**.

## State Encoding

| Name       | Bits (`Q2 Q1 Q0`) | Description                          |
|:----------:|:-----------------:|:-------------------------------------|
| Idle       | `000`             | Await 1st code + ENTER               |
| First      | `001`             | 1st code correct; await 2nd + ENTER  |
| Second     | `010`             | 2nd code correct; await biometric    |
| Access     | `011`             | Biometric OK                         |
| Incorrect  | `100`             | Any wrong code/biometric             |

## Transition Table

| Present State (`Q2Q1Q0`) | `code_right` | `bio_right` | Next State (`Q2Q1Q0`) |
|:-------------------------:|:------------:|:-----------:|:------------------------:|
| **000 (Idle)**            | 1            | x           | **001 (First)**          |
|                           | 0            | x           | **100 (Incorrect)**      |
| **001 (First)**           | 1            | x           | **010 (Second)**         |
|                           | 0            | x           | **100 (Incorrect)**      |
| **010 (Second)**          | x            | 1           | **011 (Access Granted)** |
|                           | x            | 0           | **100 (Incorrect)**      |
| **011 (Access)**          | x            | x           | **000 (Idle)**           |
| **100 (Incorrect)**       | x            | x           | **000 (Idle)**           |

- The next **ENTER** press is fed to all D-FF clock inputs, and also acts as a synchronous reset from **Access** or **Incorrect** back to **Idle**.
