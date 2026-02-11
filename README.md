==============================================================================
FDSP MODULE SPEC — GAMMA DSP v3
==============================================================================

FDSP Module: <name>
FILENAME: <core>_<name>.py

You are generating a **Gamma DSP module** using **NumPy + Numba JIT**.

This module follows the same architectural rules and style as existing Gamma DSP
modules (delay cores, oscillators, FM cores, filters, envelopes).

NumPy is flexible — you MAY mix tick DSP, block DSP, and spectral DSP.

------------------------------------------------------------------------------

ABSOLUTE RULES — NOT NEGOTIABLE
-------------------------------
>>> DSP math must NEVER use classes, dicts, lists, PyTrees, or Python objects.
>>> DSP state must use ONLY: NumPy arrays, scalars, and tuples.
>>> Numba is MANDATORY for DSP kernels (unless explicitly exempted).
>>> DSP functions are PURE FUNCTIONAL.

------------------------------------------------------------------------------

DSP CORE REQUIREMENTS
--------------------
• DSP functions:
      (state, parameters...) → (output, new_state)

• DSP state:
      Always a tuple of NumPy arrays and/or scalars.
      Layout is fixed and identical across all DSP functions.

• Parameters:
      Passed ONLY as individual scalars or per-sample arrays.
      NEVER bundled, NEVER stored in objects.

• Arrays in DSP state:
      May be mutated in-place
      Must NEVER be reallocated inside DSP

• No dynamic allocation inside DSP loops
• No helper functions that mix arrays and scalars
• No hidden configuration
• No parameter objects

------------------------------------------------------------------------------

PROCESSING MODEL
----------------
Tick DSP is the **reference implementation**.

Block DSP:
    Must execute the **exact same DSP math**
    by looping the tick math inside a Numba kernel.

No alternate math. No simplifications.

Spectral DSP is allowed and may define its own math.

------------------------------------------------------------------------------

SUPPORTED DSP MODES
-------------------
You MAY implement any combination:

1) Tick / time-varying
       <name>_tick(state, ...) → (y, new_state)

2) Block / constant parameters
       <name>_process_block(state, N, ..., out=None)

3) Block / time-varying parameters
       <name>_process_block_tv(state, ..., out=None)

------------------------------------------------------------------------------

NUMBA RULES (MANDATORY)
----------------------
All DSP kernels:
      @njit(cache=True, fastmath=True)

No Python objects inside jitted DSP.
No dynamic allocation inside jitted loops.
All arrays preallocated before entering DSP kernels.

Python fallback is allowed only if it reproduces DSP math exactly.

------------------------------------------------------------------------------

FILE STRUCTURE (STRICT ORDER)
-----------------------------
1. Header comment describing behavior & DSP math
2. Imports (numpy, numba, matplotlib, sounddevice, etc.)
3. <name>_init(...)
4. <name>_update_state(...)
5. <name>_tick(...)                      [optional]
6. <name>_process_block(...)
7. <name>_process_block_tv(...)          [optional]
8. Optional thin wrapper class (NO DSP inside)
9. __main__ smoke tests
10. Analytical plots (structure, response, stability)
11. Listening tests (mandatory if audio)
12. Audio tests (sounddevice)

------------------------------------------------------------------------------

EXECUTION & TESTING RULES
------------------------
• Importing the module must be safe and silent
• All plots, audio, tests exist ONLY under:
      if __name__ == "__main__":

------------------------------------------------------------------------------

SPEC TO FILL
------------

MODULE NAME:
<insert>

DESCRIPTION:
<insert>

OPERATING MODES:
- Tick: yes/no + explanation
- Block: yes/no + explanation
- Hybrid: yes/no

INPUTS:
- <name> : <meaning, scalar or array>

OUTPUTS:
- <name> : <meaning>

STATE (tuple):
(state_0, state_1, ...)

DSP MATH:
• Tick equations
• Block equations
• Time-varying rules
• Nonlinearities
• Interpolation
• Stability rules

NOTES:
Special constraints or behaviors.

------------------------------------------------------------------------------

INSTRUCTIONS
------------
After filling in the spec EXACTLY:

1) Generate the complete standalone Python module
2) Obey every architectural rule above
3) Append an "IMPROVEMENTS / IDEAS" section as Python comments at the END
