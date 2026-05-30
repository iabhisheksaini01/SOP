# Production OPCO – Service Account Token Update Tracker

**Objective:** Update Production OPCOs automation scripts to use dedicated Service Account based authentication with long-lived tokens instead of existing/root kubeconfig credentials.

| S.No | OPCO              | IP Overlapping Script | Calico Restart Script | Status    | Remarks                                                      |
| ---- | ----------------- | --------------------- | --------------------- | --------- | ------------------------------------------------------------ |
| 1    | Uganda            | ✅ Updated             | ✅ Updated             | Completed | Service Account, token and kubeconfig updated and validated. |
| 2    | Rwanda            | ✅ Updated             | ✅ Updated             | Completed | Service Account, token and kubeconfig updated and validated. |
| 3    | Kenya             | ⬜ Pending             | ⬜ Pending             | Pending   | Yet to be updated.                                           |
| 4    | Tanzania          | ⬜ Pending             | ⬜ Pending             | Pending   | Yet to be updated.                                           |
| 5    | Zambia            | ⬜ Pending             | ⬜ Pending             | Pending   | Yet to be updated.                                           |
| 6    | Malawi            | ⬜ Pending             | ⬜ Pending             | Pending   | Yet to be updated.                                           |
| 7    | Chad              | ⬜ Pending             | ⬜ Pending             | Pending   | Yet to be updated.                                           |
| 8    | Niger             | ⬜ Pending             | ⬜ Pending             | Pending   | Yet to be updated.                                           |
| 9    | Gabon             | ⬜ Pending             | ⬜ Pending             | Pending   | Yet to be updated.                                           |
| 10   | Congo Brazzaville | ⬜ Pending             | ⬜ Pending             | Pending   | Yet to be updated.                                           |
| 11   | DRC               | ⬜ Pending             | ⬜ Pending             | Pending   | Yet to be updated.                                           |
| 12   | Madagascar        | ⬜ Pending             | ⬜ Pending             | Pending   | Yet to be updated.                                           |
| 13   | Seychelles        | ⬜ Pending             | ⬜ Pending             | Pending   | Yet to be updated.                                           |

## Activity Details

* Created dedicated Service Accounts for automation activities.
* Generated long-lived Service Account tokens for Production clusters.
* Created new kubeconfig files using Service Account credentials.
* Updated both IP Overlapping and Calico Restart automation scripts to use the new kubeconfig.
* Validated cluster access and successful script execution post-update.

## Current Progress

**Completed:** 2/13 Production OPCOs (Uganda, Rwanda)

**Pending:** 11/13 Production OPCOs
