# Script Management #
## Motivation ##
* Change is inevitable
    * Symphony updates may introduce breaking changes to customer scripts
        * Schema changes
        * Changes to data composition
    * Common Library (CL) updates may introduce breaking changes to scripts that are dependent on the CL
* What is our goal?
    * Prevent breaking changes?
    * Notification of need to test client scripts as a result of Symphony or CL updates?
    * Minimize the amount of testing required by the client when Symphony or the CL are updated?
* How should we deliver scripts?
    * In the past we have treated scripts as individual items to be delivered
        * Scripts that were dependent on the CL were delivered with a script to install the CL
    * The most recent set of scripts for Riachuelo were delivered as a single package containing the CL
## Options for managing script changes ##
* Snapshot delivery
    * The CL is versioned in SVN
    * A snapshot of all of the customer scripts (dependent on the CL or all scripts) is kept as a single package including the CL
        * We should probably differentiate between 'fix' scripts (scripts that are run once to modify the database or fix data) and operational (scripts that are run regularly and perform some business logic). It is probably unnecessary to maintain fix scripts over snapshot versions.
    * When a new script is commissioned that is dependent on the CL, a decision will be made whether to use the customer's version of the CL (from the snapshot) or to update the customer to the most recent version of the CL. In the event that it is decided to update the CL then an analysis of how much testing on existing scripts is required may be done. If the existing scripts need to be tested then it needs to be decided who's responsibility is it to test.
* Script versioning
    * There a need to know when the version of a dependency has changed. If we know the versions of the dependencies we can deduce whether an existing script needs to be retested because of an update to one or more of it's dependencies
    * Stored Procedures, User Defined Functions and User Defined Types are not versioned in SQL. Any versioning needs to be implemented externally.
        * Table of SQLS objects and their assigned versions
        * Table of dependencies and their versions
        * Requires a setup script that installs the current version and records the metadata.
        * Requires a procedure to evaluate the version dependencies and warn about changes.
    * Limitations
        * This only works if the client uses the setup script to manage the SQLS objects.
        * This approach is more suited to procedures, functions and types than scripts. Scripts are not stored in the database, the only reason to use a setup script is to add the necessary version metadata as opposed to the other objects which need a setup script or scripts to add them to the database. This is somewhat unnatural and harder to enforce.
        * If the customer/implementor don't follow the process exactly than the system will not achieve its goals. This makes the process brittle.
## General Limitations ##
* We have no control over the script after it has been delivered; customers may make changes without our knowledge.
* There is no way to enforce the necessary behavior with either approach, however with the snapshot approach the responsibility for working 'properly' is on us and not the customer.
