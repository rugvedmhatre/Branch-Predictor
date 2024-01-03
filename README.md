# Branch-Predictor
A two-level branch predictor featuring a 2-bit saturating counter. This simulator operates on a branch instruction trace file, detailing the program counter (PC) for each branch and its outcome—Taken (1) or Not Taken (0). Upon simulation completion, the output file denotes the prediction outcomes for each branch within the trace.

## Two-Level Branch Predictor

The design consists of a PHT that has $2^m$ 2-bit saturating counter, and a
global BHT that has $2^h$ w-bit branch history. Each saturating predictor in the PHT is initialized in 2-bit `10` (Predict Weak Taken) state and is updated as per a finite state machine.

The value of m is specified in the `config.txt`.

| 00 | 01 | 10 | 11 |
|---|---|---|---|
| Strong Not Taken | Weak Not Taken | Weak Taken | Strong Taken |

Each BHT entry is initialized to 0.

![Branch Predictor Architecture](https://github.com/rugvedmhatre/Branch-Predictor/blob/main/branch-predictor-architecture.png)

We will start from the branch address (PC) to access the saturating counter for a prediction. Here are more details for the figure:

1. The BHT is indexed using h bits from the PC, starting from the 3rd LSB.
The BHT has $2^h$ entries with each entry having w bits.
2. The w bits from the BHT are concatenated with (m-w) bits from the PC
(again starting from the 3rd LSB) as shown in the figure.
3. We use these m bits to index into the PHT which has $2^m$ entries. Each PHT entry gives us our 2-bit prediction for the current branch. Both Strong Not Taken (00) and Weak Not Taken (01) mean we predict the branch to be Not Taken. On the other hand, both Strong Taken (11) and Weak Taken (10) indicate that we predict the branch to be Taken.
4. Compare the prediction with the actual branch action as provided in the
trace file. If the branch was indeed taken, update the PHT entry towards the taken side. If the branch was not taken, update the PHT entry towards the not taken side. Example: Suppose our PHT entry was 01 (Weak Not Taken) and the actual branch action was Taken (1). We would update the PHT entry to be 10 (Weak Taken).
5. Update the BHT. Example: Suppose the current BHT entry is 010 and the
branch action is Not Taken (0), then you need to update the BHT entry to 100 by
left-shifting the BHT and placing 0 in the LSB.

**Config File**

The config file config.txt contains 3 lines with the value of m, h, w.

**Trace File**

The trace file, trace.txt, contains one branch per line. Each line has the branch address (PC) for the corresponding branch (in hex format) followed by a single bit indicating Taken (1) or Not Taken (0). A sample trace file is provided.

**Output Format**

The output from the simulator is a file trace.txt.out that has one line per
branch. Each line has a single bit that indicates whether that branch was predicted as Taken (1) or Not Taken (0).

