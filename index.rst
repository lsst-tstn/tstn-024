:tocdepth: 1

.. Please do not modify tocdepth; will be fixed when a new Sphinx theme is shipped.

.. sectnum::

.. note::

   This tech note details some basic concepts about how users will interact with the Vera Rubin Observatory control system during commissioning and operations.


.. _Introduction:

Introduction
============

The main goal of the observatory control system is to provide a user-friendly framework with a high level of flexibility.
During commissioning, engineering and operations there will be situations where users will want to execute non-standard, customized operations.
The goal of the observatory control system is not only to allow users to perform those custom tasks but also to be able to incorporate them to the system.
The main points that drives the development of the observatory control system are:

  - Regular operations routines should be easily accessible;
  - Highly customizable scripting capability;
  - User sandbox space, to allow development of routines on the fly;
  - Capability to incorporate user-developed features.

To accomplish the aforementioned goals the observatory system is equipped with two main components; the :ref:`ScriptQueue <Script-Queue>` and :ref:`Jupyter notebooks <Jupyter-notebooks>`.
In the following sections we will review each of these components an explore how users can take advantage of their features to accomplish different types of tasks and contribute to the observatory control system.

.. note::

   Should we have a section with a definition of what we consider users (might be a bit redundant)?

   Should we define the different levels of users and what are expected of them?

.. _Script-Queue:

Script Queue
============

The `ScriptQueue`_ is the main hub for the execution of regular operations that requires minimum interactivity.
For instance, when operators need to prepare the observatory for calibrations or for night operation, it will be possible to add a script to the ScriptQueue that will perform all the required tasks (e.g. position telescope and dome, perform required fine tuning in positioning, open mirror covers and so on).
Likewise, the ScriptQueue should be the main tool astronomers will rely on to perform regular nighttime operations, e.g., track a target and obtain a set of standard observations.

Interacting with the ScriptQueue is mainly done through :ref:`LOVE ScriptQueue user interface <fig-scriptqueueui>`.

.. figure:: /_static/ScriptQueueUI.png
   :name: fig-scriptqueueui
   :target: ../_images/ScriptQueueUI.png
   :alt: LOVE ScriptQueue user interface

   ScriptQueue LOVE user interface. ...

In general, scripts require minimum interaction to be executed except, of course, for the occasional configuration.
A database of configuration will be available for users to execute the most common set of operations and they also have the possibility of editing configuration on the user interface, which also provides schema validation.

Regular operations are separated into two distinct groups of `SAL Scripts`_; standard and external.

`Standard Scripts`_ hosts production-level operations, that are well tested and understood.
They must strictly follow the `development guidelines`_ and are subject to rigorous code review.

`External Scripts`_, on the other hand, works as a staging and user sandbox area for `SAL Scripts`_.
Following the `development guidelines`_ on this package is still recommended (but not strictly enforced) and code is subjected to less rigorous code review.

Additional details about the classification of different levels of operations can be found in `tstn-010`_, as well as guidelines on how to contribute.

.. _ScriptQueue: https://ts-scriptqueue.lsst.io
.. _SAL Scripts: https://ts-salobj.lsst.io/sal_scripts.html
.. _Standard Scripts: https://github.com/lsst-ts/ts_standardscripts
.. _External Scripts: https://github.com/lsst-ts/ts_standardscripts
.. _development guidelines: https://tssw-developer.lsst.io

In general, using the ScriptQueue requires some familiarity with the observatory system and minimum software development skills.
Users should be able to, after inspecting the LOVE status screens, determine the state of the observatory and its readiness to perform certain types of operation.
Other than that, some knowledge about `yaml`_ and `json schema`_ may be useful for writing and inspecting script configuration, though :ref:`LOVE <fig-scriptqueueui>` will have features to help understanding and validating them.

.. _yaml: https://yaml.org/spec/1.2/spec.html
.. _json schema: http://json-schema.org

.. _Jupyter-notebooks:

Jupyter notebooks
=================

The notebook server available at the summit control network is built on top of the `DM science platform`_, augmented with `Telescope and Site observatory control package`_.
They allow users to combine observatory control activities with data analysis in a highly interactive we-based interface.

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
