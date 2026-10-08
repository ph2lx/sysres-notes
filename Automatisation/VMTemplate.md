,  

    "SRV32_tec_PRO_BASTION_ADMT1-MARTIN" = {  
        vm_name       = "SRV32_tec_PRO_BASTION_ADMT1-MARTIN"  
        vm_hostname   = "SRV32"  
        vm_ip_address = "10.20.17.27"  
        vm_gateway    = "10.20.17.254"  
        template_name = "/SITEA/vm/Templates/Nouveau DC/2K19 - Bastion Technique" # Linux  
        network_name   = "DC_Adm"  
        vm_folder      = "/PRODPRE/VM APPLIS"  
        cpu = 2  
        ram = 4096  
        site = "SITEA"  
        type = "APPLIS"  
        disks = [ {label="disk0", size=60} ]  
    }  
