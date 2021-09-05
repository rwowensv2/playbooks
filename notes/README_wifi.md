sudo modprobe -r iwlwifi && sudo modprobe iwlwifi lar_disable=Y

sudo -i
echo "options iwlwifi lar_disable=Y"  >>  /etc/modprobe.d/iwlwifi.conf


#other
lsmod | grep wifi
iwlwifi               223575  1 iwlmvm
cfg80211              632335  3 iwlwifi,mac80211,iwlmvm

$ iwconfig
