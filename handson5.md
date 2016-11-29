1. transaction2 havent commit yet
2. redo:
* Account: studentC Value: 3100
* Account: studentA Value: 900

3. crash happened before studentc created
4. db should be 
* studentC 3100
* studentA 900

5. Yes
6. meaning
* winner for actions that be recovered and be redone
* loser for actions that have no"commit" log, and he undone
* done for actions that have been done before the crash

7. why? havent commit
8. delete the loser ?
9. no 
10. 
11. 
Beginning id: 1
> Initializing account studentA to 1000
> Committing id: 1
> Ending id: 1
> Beginning id: 2
> Initializing account studentB to 2000
> Doing checkpoint
> Beginning id: 3
> Initializing account studentC to 3000
> Crediting account studentC by 100
> Debiting account studentB by 100
> Committing id: 3
> 
-----------------------
On-disk DB contents:
Account: studentA Value: 1000
-----------------------

-----------------------
LOG contents:
type: START action_id: 1
type: UPDATE action_id: 1 variable: studentA redo: "1000" undo: NULL
type: OUTCOME action_id: 1 status: COMMITTED
type: END action_id: 1
type: START action_id: 2
type: UPDATE action_id: 2 variable: studentB redo: "2000" undo: NULL
type: CHECKPOINT PENDING: id: 2 COMMITTED:  DONE: id: 1 
type: START action_id: 3
type: UPDATE action_id: 3 variable: studentC redo: "3000" undo: NULL
type: UPDATE action_id: 3 variable: studentC redo: "3100" undo: "3000"
type: UPDATE action_id: 2 variable: studentB redo: "1900" undo: "2000"
type: OUTCOME action_id: 3 status: COMMITTED
-----------------------
> crashing ...
hex@hex-X450JF:~/下载$ ./wal-sys.py
Recovering the database ..
Starting rollback ...
  The log was rolled back 6 lines
Rollback done
Winners: id: 3  Losers: id: 2  Done: id: 1 
Starting forward scan ...
  REDOING: type: UPDATE action_id: 3 variable: studentC redo: "3000" undo: NULL
  REDOING: type: UPDATE action_id: 3 variable: studentC redo: "3100" undo: "3000"
  Logging END records for winners
Forward scan done
Recovery done
> show_state 

-----------------------
On-disk DB contents:
Account: studentC Value: 3100
Account: studentA Value: 1000
-----------------------

-----------------------
LOG contents:
type: START action_id: 1
type: UPDATE action_id: 1 variable: studentA redo: "1000" undo: NULL
type: OUTCOME action_id: 1 status: COMMITTED
type: END action_id: 1
type: START action_id: 2
type: UPDATE action_id: 2 variable: studentB redo: "2000" undo: NULL
type: CHECKPOINT PENDING: id: 2 COMMITTED:  DONE: id: 1 
type: START action_id: 3
type: UPDATE action_id: 3 variable: studentC redo: "3000" undo: NULL
type: UPDATE action_id: 3 variable: studentC redo: "3100" undo: "3000"
type: UPDATE action_id: 2 variable: studentB redo: "1900" undo: "2000"
type: OUTCOME action_id: 3 status: COMMITTED
type: END action_id: 3
-----------------------

8-9::

hex@hex-X450JF:~/下载$ ./wal-sys.py
Recovering the database ..
Starting rollback ...
  The log was rolled back 7 lines
Rollback done
Winners:   Losers: id: 2  Done: id: 1 id: 3 
Starting forward scan ...
  Logging END records for winners
Forward scan done
Recovery done
>show_state
-----------------------
On-disk DB contents:
Account: studentC Value: 3100
Account: studentA Value: 1000
-----------------------

-----------------------
LOG contents:
type: START action_id: 1
type: UPDATE action_id: 1 variable: studentA redo: "1000" undo: NULL
type: OUTCOME action_id: 1 status: COMMITTED
type: END action_id: 1
type: START action_id: 2
type: UPDATE action_id: 2 variable: studentB redo: "2000" undo: NULL
type: CHECKPOINT PENDING: id: 2 COMMITTED:  DONE: id: 1 
type: START action_id: 3
type: UPDATE action_id: 3 variable: studentC redo: "3000" undo: NULL
type: UPDATE action_id: 3 variable: studentC redo: "3100" undo: "3000"
type: UPDATE action_id: 2 variable: studentB redo: "1900" undo: "2000"
type: OUTCOME action_id: 3 status: COMMITTED
type: END action_id: 3
-----------------------


11:::
-----------------------
On-disk DB contents:
Account: studentA Value: 1000
-----------------------

-----------------------
LOG contents:
type: START action_id: 1
type: UPDATE action_id: 1 variable: studentA redo: "1000" undo: NULL
type: OUTCOME action_id: 1 status: COMMITTED
type: END action_id: 1
type: START action_id: 2
type: UPDATE action_id: 2 variable: studentB redo: "2000" undo: NULL
type: CHECKPOINT PENDING: id: 2 COMMITTED:  DONE: id: 1 
type: START action_id: 3
type: UPDATE action_id: 3 variable: studentC redo: "3000" undo: NULL
type: UPDATE action_id: 3 variable: studentC redo: "3100" undo: "3000"
type: UPDATE action_id: 2 variable: studentB redo: "1900" undo: "2000"
type: OUTCOME action_id: 3 status: COMMITTED
-----------------------
> show_state
crashing ...
hex@hex-X450JF:~/下载$ ./wal-sys.py -undo
Recovering the database ..
Starting rollback ...
  UNDOING: type: UPDATE action_id: 2 variable: studentB redo: "1900" undo: "2000"
  UNDOING: type: UPDATE action_id: 2 variable: studentB redo: "2000" undo: NULL
  The log was rolled back 8 lines
Rollback done
Winners: id: 3  Losers: id: 2  Done: id: 1 
 Logging STATUS records for losers
Recovery done
> show_state

-----------------------
On-disk DB contents:
Account: studentA Value: 1000
-----------------------

-----------------------
LOG contents:
type: START action_id: 1
type: UPDATE action_id: 1 variable: studentA redo: "1000" undo: NULL
type: OUTCOME action_id: 1 status: COMMITTED
type: END action_id: 1
type: START action_id: 2
type: UPDATE action_id: 2 variable: studentB redo: "2000" undo: NULL
type: CHECKPOINT PENDING: id: 2 COMMITTED:  DONE: id: 1 
type: START action_id: 3
type: UPDATE action_id: 3 variable: studentC redo: "3000" undo: NULL
type: UPDATE action_id: 3 variable: studentC redo: "3100" undo: "3000"
type: UPDATE action_id: 2 variable: studentB redo: "1900" undo: "2000"
type: OUTCOME action_id: 3 status: COMMITTED
type: OUTCOME action_id: 2 status ABORTED
-----------------------

