.. _network_configuration:

*******
Network
*******

.. index:: network

This section describes how to configure additional network devices that may be used to better
accomodate more complex network infrastructures. Network interfaces are managed in the XiVO web
interface via the page :menuselection:`Configuration --> Network --> Interfaces`.

XiVO offers 2 types of interfaces: `VoIP` and `Data`. The `VoIP` interface is used by the DHCP
server, provisioning server, and phone devices connected to your XiVO. These services will use the
information provided on the `VoIP` interface for their configuration.  For example, the DHCP server
will only listen on the VoIP interface by default.

To change these settings, you must either create a new interface or edit an existing one and change
its type.  When adding a new `VoIP` interface, the type of the old one will automatically be changed
to `Data`.


Configuring a physical interface
--------------------------------

In this example, we'll add and configure the **eth1** network interface on our XiVO.

First, we see there's already an unconfigured network interface named **eth1** on our system:

.. figure:: images/netiface_list_post_wizard.png

   :menuselection:`Configuration --> Network --> Interfaces`

To add and configure it, we click on the small plus button next to it, and we get to this page:

.. figure:: images/netiface_edit_physical_empty.png

   :menuselection:`Configuration --> Network --> Interfaces --> eth1 --> Add`

In our case, since we want to configure this interface with static information (i.e. not via DHCP),
we fill the following fields:

.. figure:: images/netiface_edit_physical_filled.png
 
   :menuselection:`Configuration --> Network --> Interfaces --> eth1 --> Add`

Note that since our **eth0** network interface already has a default gateway,
we do not enter information in the ``Default gateway`` field for our **eth1** interface.

Once the changes have been saved, the action **Apply network configuration** will appear in bold.
This action must be clicked in order for the changes to take effect.

.. figure:: images/netiface_notify_change.png
   :figclass: align-center

   Apply after modify interface


.. index:: single:VLAN

Adding a VLAN interface
-----------------------

In this example, the XiVO already has 2 network interfaces configured:

.. figure:: images/netiface_list_configured.png

   :menuselection:`Configuration --> Network --> Interfaces`

Listing the network interfaces

To add and configure a new VLAN interface, we click on the small plus button in the top right
corner,

.. figure:: images/utils_add_button.png
   :figclass: align-center
   
   :menuselection:`Configuration --> Network --> Interfaces --> Add button`

and we get to this page:

.. figure:: images/netiface_add_virtual_empty.png
   :figclass: align-center
   
   :menuselection:`Configuration --> Network --> Interfaces --> Add`

In our case, since we want to configure this interface with static information:

.. figure:: images/netiface_add_virtual_filled.png
   :figclass: align-center

   :menuselection:`Configuration --> Network --> Interfaces --> Add`

Click on **Save** list the network interfaces:

.. figure:: images/netiface_list_new_virtual.png
   :figclass: align-center
      
   :menuselection:`Configuration --> Network --> Interfaces`

- The new virtual interface has been successfully created.

.. note:: 
   Do not forget after you finish the configuration of the network to apply it with the button: 
   **Apply network configuration**

After applying the network configuration:

.. figure:: images/netiface_list_virtual_after_apply.png
   :figclass: align-center

   Network configuration successfully apply


Add static network routes
-------------------------

Static routes cannot be added via the web interface. However, you may add static routes to your XiVO
by following following the steps described below. This procedure will ensure that your static routes
are applied at startup (i.e.  each time the network interface goes up).

#. Create the file :file:`/etc/network/if-up.d/xivo-routes`::

    touch /etc/network/if-up.d/xivo-routes
    chmod 755 /etc/network/if-up.d/xivo-routes

#. Insert the following content::

    #!/bin/sh

    if [ "${IFACE}" = "<network interface>" ]; then
        ip route add <destination> via <gateway>
        ip route add <destination> via <gateway>
    fi

#. Fields <network interface>, <destination> and <gateway> should be replaced by your specific
   configuration.  For example, if you want to add a route for 192.168.50.128/25 via 192.168.17.254
   which should be added when eth0 goes up::
    
    #!/bin/sh

    if [ "${IFACE}" = "eth0.2" ]; then
        ip route add 192.168.50.128/25 via 192.168.17.254
    fi

.. note:: The above check is to ensure that the route will be applied only if the correct interface
   goes up.  This check should contain the actual name of the interface (i.e. `eth0` or `eth0.2` or
   `eth1` or ...).  Otherwise the route won't be set up in every cases.


Change interface MTU
--------------------

.. warning::
   Manually changing the MTU is risky. Please only proceed if you are aware of what you are doing.


These steps describe how to change the MTU::

#. Create the file :file:`/etc/network/if-up.d/xivo-mtu`::

   touch /etc/network/if-up.d/xivo-mtu
   chmod 755 /etc/network/if-up.d/xivo-mtu

#. Insert the following content::

    #!/bin/sh

    # Set MTU per iface
    if [ "${IFACE}" = "<data interface>" ]; then
        ip link set ${IFACE} mtu <data mtu>
    elif [ "${IFACE}" = "<voip interface>" ]; then
        ip link set ${IFACE} mtu <voip mtu>
    fi

#. Change the *<data interface>* to the name of your interface (e.g. eth0), and the *<data mtu>* to
   the new MTU (e.g. 1492),
#. Change the *<voip interface>* to the name of your interface (e.g. eth1), and the *<voip mtu>* to
   the new MTU (e.g. 1488)

.. note::
   In the above example you can set a different MTU per interface.
   If you don't need a per-interface MTU you can simply write::

     #!/bin/sh

     ip link set ${IFACE} mtu <my mtu>

