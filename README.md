# netscaler-yml-config

# Overview
This script processes a YAML file that contains a Netscaler state and applies it to one or more appliances.  It requires the NSNitro module to be installed.  This can be done via the following command on most systems:
````
pip install nsnitro
````

It has been tested with Python 2.7.

# Usage
The script accepts the filename of a YAML-based configuration ("ns.yml" for example)
````
python apply_netscaler_state.py <yaml_filename>
````
# Output
A log file is produced in the working directory each time the script is executed.

# Supported States
## Servers
Server definition for Load Balancing
* Name
* IP Address

## Service Groups
Service groups for Load Balancing
* Name
* Protocol
* Server bindings to Service Groups
  * Server Name
  * Port

## Load Balancing
Virtual server for Load Balancing
* Name
* Virtual IP Address
* Port
* Protocol
* Service Group Bindings

## Content Switching
Virtual server for Content Switching
* Name
* Virtual IP Address
* Port
* Protocol
* Content Switching Policy Bindings
* Default Load Balancing Virtual Server

## Content Switching Policies
* Name
* Expression
* Action

## Content Switching Actions
* Name
* Target Load Balancing Virtual Server

# YAML Schema
The format of the YAML is hierarchical and relates to the logical state of the managed resources.  Components relationships are defined using the 'name' key.

````
ns_groups:  #Contains a list of one or more NetScaler groups, each with an independent configuration
  - name: <ns_group_name>
    ns_instance:
     address: <nsip_address or hostname>
     user: <ns_user>
     pass: <ns_password>
    
    service_groups:  # Contains a list of one or more Load Balancing Service Group definitions
      - name: <service_group_name>
        servers:  # Contains one or more servers to include in the service group
        - name: <server_name>  # Reference to Server name contained in Server definition
          port: <port>
        protocol: <protocol_name>  # Service type e.g. HTTP
    
     servers:  # Contains a list of one or more Load Balacing Server definitions
       - name: <server_name>
         ip_address: <ip>

     lbvservers:  # Contains a list of one or more Load Balancing Virtual Server definitions
       - name: <lbvserver_name>
         vip_address: <lbvserver_ip_address>  # Virtual IP Address of lbvserver
         port: <port>
         protocol: <protocol_name>  # Service type e.g. HTTP
         service_group_bindings:  # Contains a list of one or more Service Groups to bind the lbvserver to
           - <service_group_name>  # Reference to Service Group name contained in Service Group definition
      
      csvservers:  # Contains a list of one or more Content Switching Virtual Server definitions
        - name: <csvserver_name>
          vip_address: <csvserver_ip_address>  # Virtual IP Address of csvserver
          port: <port>
          protocol: <protocol_name> # Service type e.g. HTTP
          policy_bindings:  # Contains a list of one or more Content Switching policy bindings
            - name: <cs_policy_name>  # Reference to CS Policy name contained in CS Policy definition
              priority: <priority>  # Integer value of policy binding e.g. 100
          default_lbvserver: <lbvserver_name>  # Reference to LBVServer name contained in LBVServer definition
          
      cs_policies:  # Contains a list of one or more Content Switching Policy definitions
        - name: <cs_policy_name>
          expression: <ns_policy_expression>  # Valid NetScaler policy expression
          action: <cs_action_name>  # Reference to CS Action name contained in CS Action definition
          
      cs_actions:  # Contains a list of one or more Content Switching Action definitions
        - name: <cs_action_name>
          target_lbvserver: <lbvserver_name>  # Reference to LBVServer name contained in LBVServer definition
````
              
            
         
