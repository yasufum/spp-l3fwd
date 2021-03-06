..  SPDX-License-Identifier: BSD-3-Clause
    Copyright(c) 2010-2014 Intel Corporation

.. _spp_vf_overview:

Overview
========

This section describes an overview of SPP's extensions, ``spp_vf`` and
``spp_mirror``.
SPP provides a connectivity between DPDK processes as a virtual patch panel.
However, It is not sufficient considering more practical usecases.
For instance, you might need a classification for incoming packets if you have
multiple processes as destinations. You might also need to duplicate packets
for monitoring or inspection.

``spp_vf`` is a SPP secondary process for forwarding packets to multiple
destination ports with a MAC address classification. It is also supported to
merge from several source ports to aggregate packets to a destination port.

``spp_mirror`` is an another kind of secondary process to duplicate incoming
packets. This feature is intended to use as a
`TaaS
<https://docs.openstack.org/dragonflow/latest/specs/tap_as_a_service.html>`_
feature for port mirroring introduced in OpenStack.


spp_vf
------

``spp_vf`` provides a SR-IOV like network feature.

``spp_vf`` forwards incoming packets to several destination VMs by referring
MAC address like as a Virtual Function (VF) of SR-IOV.

``spp_vf`` is a  multi-process and multi-thread application.
Each of ``spp_vf`` has one manager thread and worker threads called as
components.
The manager thread provides a function for parsing a command and creating the
components.
The component threads have its own multiple components, ports and classifier
tables including Virtual MAC address.

This is an example of network configuration, in which one
``classifier_mac``,
one merger and four forwarders are running in ``spp_vf`` process
for two destinations of vhost interface.
Incoming packets from rx on host1 are sent to each of vhosts of VM
by looking up destination MAC address in the packet.

.. figure:: ../images/spp_vf/spp_vf_overview.*
    :width: 70%

    Overview of spp_vf


spp_mirror
----------

``spp_mirror`` is an implementation of
`TaaS
<https://docs.openstack.org/dragonflow/latest/specs/tap_as_a_service.html>`_
as a SPP secondary process for port mirroring.
TaaS stands for TAP as a Service.

``spp_mirror`` consists of one manager thread and worker threads as similar to
``spp_vf``, but just one worker role for mirroring.
:numref:`figure_spp_mirror_overview` shows an overview of use of ``spp_mirror``
in which ``spp_mirror`` process receives packets from ``ring:0`` in ``spp_vf``
for duplicating the packets for monitoring.
Worker thread ``mirror`` has two of tx rings for forwaring the packets to the
destination and a monitor application running on VM3.

.. _figure_spp_mirror_overview:

.. figure:: ../images/spp_vf/spp_mirror_overview.*
   :width: 70%

   Overview of spp_mirror
