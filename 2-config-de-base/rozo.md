!-- Tout doux ! --!

# 2.3 - Configuration réseau de base

## Principe
Pour communiquer avec le réseau local et avec le monde extérieur, votre machine a besoin :
+ D'une adresse IP
+ D'un masque de sous-réseau
+ De l'adresse IP de sa passerelle par défaut
+ Pour pouvoir résoudre des noms de domaine, de l'adresse d'au moins 1 serveur DNS.

## Config manuelle avec ip
La commande `ip` vous permet d'inspecter et modifier manuellement votre config réseau. Les modifications sont **temporaires**.

+ `ip address` : affiche les paramètres de niveau 2 et 3 pour toutes vos interfaces
    - `ip a` : raccourci
    - Une interface filaire **ethernet** commence par un `e`, par exemple :
        * `eth0`
        * `ens3`
        * `enp37s0`
    - `ip a show dev <iface>` : montrer les paramètres pour une interface donnée
    - `ip link` pour ne montrer que les paramètres de couche 2
+ `sudo ip address add <address>/<mask> dev <iface>` : ajouter une adresse IPv4 à une interface
    - `sudo ip address del <address>/<mask> dev <iface>` : supprimer une adresse IPv4
    - Exemple : 
        * ```lua
            sudo ip a add 192.168.1.10/24 dev enp37s0
            sudo ip a del 192.168.1.10/24 dev enp37s0
            sudo ip -6 a add 2001:db8:cafe::/64 dev enp37s0     # ipv6 (-6)
            ```
        * Vous pouvez aussi indiquer le masque au format décimal pointé : `sudo ip address add 192.168.1.10 255.255.255.0 dev enp37s0`
    - *NB :*
      - *Une même interface peut avoir plusieurs adresses IP*
      - *Une même machine peut avoir plusieurs interfaces (et donc aussi évidemment avoir plusieurs adresses IP)*
        - *Une même machine peut avoir des IP dans des réseaux différents (pense à un routeur)*
+ `ip route` : affiche la table de routage
+ `ip route add <ip>/<mask> via <ip>` : Ajouter une route
    - Exemple :
        ```lua
            sudo ip route add 172.16.1.0/24 via 192.168.1.254
        ```
    - Remplacer `add` par `del` pour supprimer la route
    - `ip -6` pour de l'IPv6
+ `ip route add default via <ip>` : Ajouter une route par défaut (0.0.0.0/0)

