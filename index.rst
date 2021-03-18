:tocdepth: 1

.. Please do not modify tocdepth; will be fixed when a new Sphinx theme is shipped.

.. sectnum::

.. note::

   This tech note details some basic concepts about how users will interact with the Vera Rubin Observatory control system during commissioning and operations.


.. _Introduction:

Introduction
============

The main goal of the observatory control system is to provide a user-friendly framework with a high level of flexibility.
The top-level functionality specifications that drives the development of the observatory control system are:

  - Regular operations routines should be easily accessible;
  - Must include a highly customizable scripting capability must be available and able to run user-written code;
  - An interactive mode, equivalent to a user sandbox space, must be available to allow development of routines on the fly as well as debugging;
  - Capability to incorporate user-developed features.

To accomplish the aforementioned goals, the observatory system is equipped with three main components: the :ref:`LSST Observing Visualization Environment (LOVE)<LOVE>`, :ref:`ScriptQueue <Script-Queue>` and :ref:`Jupyter notebooks <Jupyter-notebooks>`.

Under the standard operational procedures used to perform the survey, the observatory will be largely driven by the Scheduler, which will designate a visit script, which consists of a series of operations that are performed via a content controlled script. The script will be executed by the scriptQueue, and the user will be able to observe (or interrupt) the progress via the LOVE interface.
It is expected that operators will be much more involved during the start-up and shutdown phases, as well as during afternoon calibrations.
In the majority of cases, the operations will be routine and are expected to be done from a high-level of the observatory control software, as is discussed further in following sections.

During integration, commissioning, and engineering time there will be situations where users will want to execute non-standard, customized operations which require an increased level of flexibility and control.
As to be expected, the additional functionality required to perform such tasks requires more in-depth knowledge of the system.
This document aims to match the types of system users with the expected operational methodologies or tools.
Furthermore, it attempts to define the level of knowledge required to use the different tools and methodologies.

The breadth of the observatory control system allows users to perform those custom tasks via an interactive environment.
There also exists a workflow on how to develop these tasks to a level where they can be incorporated into to the control system packages.

In the following sections we will review each of these components an explore how users can take advantage of their features to accomplish different types of tasks and contribute to the observatory control system.

.. note::

   Should we have a section with a definition of what we consider users (might be a bit redundant)?

   Should we define the different levels of users and what are expected of them?

.. _LOVE:

LOVE
====

Tiago insert (or link) content here.

Important to say that users will interact with basic observatory operations via LOVE, but what is actually happening on the back end is that these operations (e.g. offsets) are actually scripts that get loaded and performed.


.. _Script-Queue:

Script Queue
============

The `ScriptQueue`_ is the main hub for the execution of regular operations that requires minimum interactivity.
As mentioned above in the `LOVE`_ section, although a user-interface to perform an operation, such as a telescope offset, will be way in which a user performs the offset, the actual action will be a :ref:`SAL Script <SAL_Scripts>` being loaded and executed by the `ScriptQueue`_.
The capabilities and functionalities executed by the scriptQueue will be very large.
For instance, when operators need to prepare the observatory for calibrations or for night operation, it will be possible to add a script to the ScriptQueue that will perform all the required tasks (e.g. position telescope and dome, perform required fine tuning in positioning, open mirror covers and so on).
Likewise, the ScriptQueue should be the main tool astronomers will rely on to perform regular nighttime operations, e.g., track a target and obtain a set of standard observations.

As mentioned already, interacting with the ScriptQueue is mainly done through :ref:`LOVE ScriptQueue user interface <fig-scriptqueueui>`.

.. figure:: /_static/ScriptQueueUI.png
   :name: fig-scriptqueueui
   :target: ../_images/ScriptQueueUI.png
   :alt: LOVE ScriptQueue user interface

   ScriptQueue LOVE user interface. ...

In general, scripts require minimum interaction to be executed except, of course, for the occasional configuration.
A database of configuration will be available for users to execute the most common set of operations and they also have the possibility of editing configuration on the user interface, which also provides schema validation.

In general, using the ScriptQueue requires some familiarity with the observatory system and minimal set of software development skills.
Users should be able to, after inspecting the LOVE status screens, determine the state of the observatory and its readiness to perform certain types of operation.
Other than that, some knowledge about `yaml`_ and `json schema`_ may be useful for writing and inspecting script configuration, though :ref:`LOVE <fig-scriptqueueui>` will eventually provide features to help understanding and validating them prior to execution.

.. _yaml: https://yaml.org/spec/1.2/spec.html
.. _json schema: http://json-schema.org

.. _SAL_Scripts:

SAL Scripts
-----------

`SAL Scripts`_ are the files which contain the logic and coordination of events and CSCs that get executed by the `ScriptQueue`_.
It is not expected these will be modified during standard night-time operations.

It is also possible to execute these from a Jupyter Notebook or from the command line when required.
These files have a strict format and must contain specific information in order to be capable of execution.


Regular operational scripts are separated into two distinct groups of `SAL Scripts`_; standard and external.

`Standard Scripts`_ hosts production-level operational scripts that are well tested and understood.
They must strictly follow the `development guidelines`_ and are subject to rigorous code review.

`External Scripts`_, on the other hand, works as a staging and user sandbox area for the development `SAL Scripts`_.
Following the `development guidelines`_ on this package is still recommended (but not strictly enforced) and code is subjected to less rigorous code review.

Additional details about the classification of different levels of operations can be found in `tstn-010`_, as well as guidelines on how to contribute.

.. _ScriptQueue: https://ts-scriptqueue.lsst.io
.. _SAL Scripts: https://ts-salobj.lsst.io/sal_scripts.html
.. _Standard Scripts: https://github.com/lsst-ts/ts_standardscripts
.. _External Scripts: https://github.com/lsst-ts/ts_standardscripts
.. _development guidelines: https://tssw-developer.lsst.io



.. _Jupyter-notebooks:

Jupyter notebooks
=================

The notebook server available at the summit control network is built on top of the `DM science platform`_, augmented with `Telescope and Site observatory control package`_.
They allow users to combine observatory control activities with data analysis in a highly interactive web-based interface.

.. _nublado:
.. _DM science platform: https://nb.lsst.io
.. _Telescope and Site observatory control package: https://ts-observatory-control.lsst.io

It is important to emphasize that the notebook platform on the control network should be used mainly for activities that require controlling observatory components through the DDS middleware.
For pure data analysis activities, users should rely on other `nublado`_ instances (e.g. commissioning cluster, NCSA, etc.).

Although extremely powerful and flexible, we do not expect notebooks to be used on all situations.
These are the main situations where users are expected to resort to notebooks:

  - Executing an integration, commissioning or engineering activity that requires some level of interactivity.
    For instance, (ADD EXAMPLE).
  - Executing a custom sequence of observations that require some level of interactivity.
    (ADD EXAMPLE).
  - Developing and testing new functionality not currently supported.
  - Debugging, testing and/or improving existing functionality.
  - Investigating issues with an individual component or a group of components.

In order to take full advantage of Jupyter notebooks users must acquire some familiarity with the observatory control system.
These are some basic concepts users should make an effort to be familiar with:

  - Commandable SAL Component (CSC).
  - `SalObj`_ Python library with special emphasis in the concept of `Remote`_.
  - Some familiarity with the `Telescope and Site observatory control package`_.
  - Intermediate Python Skills.
  - Familiarity with `Python standard asyncio library`_.
  - Some familiarity with multithreading and coroutines.
  - Familiarity with git and GitHub.

.. _SalObj: https://ts-salobj.lsst.io
.. _Remote: https://ts-salobj.lsst.io/py-api/lsst.ts.salobj.Remote.html#lsst.ts.salobj.Remote
.. _Python standard asyncio library: https://docs.python.org/3.7/library/asyncio.html

.. _Notebook-repository:

Notebook repository
-------------------

The main repository to store and manage Jupyter notebooks for interacting with the Rubin Observatory control system is `ts_notebooks`_.
Details on how this repository fits into the development process can be found in `tstn-010`_.

.. _ts_notebooks: https://github.com/lsst-ts/ts_notebooks
.. _tstn-010: https://tstn-010.lsst.io

.. _User-Knowledge-Expectations:

User-Knowledge Expectations
===========================

Required software-interactions of different types of users:

    - Observing Specialists or visiting-operators

        - standard nighttime/daytime ops
        - operates via LOVE, troubleshoots via EUIs
        - Executes and makes small edits to notebooks, but is not expected to write them from scratch

            - Minimal level of Python, a few commands of git (e.g. git-checkout and git-pull)

        - Comfortable in finding and execute commands via high-level classes
        - Some knowledge of low-level CSC functionality
        - Interacts with data to perform offsetting, focus etc.

    - Commissioning Scientist/Engineer

        - Includes operator skills plus the following:
        - Extensive use of the notebook interface, including the writing of code and launching of scripts

            - Comfortable in Python, competent with git

        - Writes and executes custom external scripts
        - Able to create and load new config files
        - Not expected to write production level scripts
        - Able to switch between software versions of deployed components
        - Able to update scriptQueue repos
        - Able to diagnose issues via the EFD/Chronograf

    - Software Developer

        - Updates code of CSCs or classes
        - Writes and reviews production level scripts

            - Expert in Python and git

        - Modifies available builds
        - Manages deployment (Rancher)



Examples of Different Levels of Operations
==========================================

This can probably be incorporated into the above section, but keeping separate for now to facilitate discussion

User-Level:

    - Slewing
    - Offsetting
    - Taking an Image
    - Launching Script and editing config
    - Type of Notebook to be executed?


Commissioning Level:

    - Updating Config file
    - Standard Notebook for testing
    - Script writing example?



.. _Contributing:

Contributing
============

Occasionally, specially during early commissioning and integration activities, users may face situations where they need to perform a certain type of operation that is not possible with the available script set.
In these situations, users are highly encouraged to contribute to the main feature set.

.. note::
   I feel that this is already covered in tstn-010, so, maybe remove it?
   Or maybe just adding a quick overview?

.. .. rubric:: References

.. Make in-text citations with: :cite:`bibkey`.

.. .. bibliography:: local.bib lsstbib/books.bib lsstbib/lsst.bib lsstbib/lsst-dm.bib lsstbib/refs.bib lsstbib/refs_ads.bib
..    :style: lsst_aa
