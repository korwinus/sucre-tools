#!/bin/bash
#version:0.2.1
#changelog:\nLocation of sentinel log\nReview system log\n@reboot crontab review\nDownload Location - ability to change\nLanguage update\nRussian Language Support
NONE='\033[00m'
RED='\033[01;31m'
GREEN='\033[01;32m'
YELLOW='\033[01;33m'
PURPLE='\033[01;35m'
CYAN='\033[01;36m'
WHITE='\033[01;37m'
BOLD='\033[1m'
UNDERLINE='\033[4m'

BINDIR=/root/sucr
COIN="SUCR "
DAEMON="sucrd"
CONF="/root/.sucrcore/sucr.conf"
CLI=/usr/bin/sucr-cli
CORE=/root/.sucrcore
TOOLBIN=/usr/bin
TOOLTMP=/tmp
TOOLNAME=sucrtools
TOOLNAMENEW=sucrtoolsnew
TOOLHUB=masternodetools.com/tools
##SENTINEL_LOG="/root/sentinel-cron.log"
SENTINEL_LOG="/root/.sucrcore/sentinel/sentinel-cron.log"

CURVERSION="`cat $TOOLBIN/$TOOLNAME | grep -m1 \#version: | awk '{print $1}'| cut -f2 -d:`"
SCANPORT="" ## include port if needed while scanning external ip configuration
LANGUAGE="`cat $CORE/$TOOLNAME.conf | grep LANGUAGE=`"
GREPSYSLOG="sucr"
WALLETSTART=""

ShowFirewall() {
     echo -e  "$firewall"
     #"\n${YELLOW}Display firewall rules${NONE}\n  Using the command:\n   ufw status numbered\n"
     ufw status numbered
     echo
}

ShowSentinel() {
    DaemonMustBeActive
    if [ "$TOT_DAEMON" = "2" ]
    then
      SENTINEL_DEBUG=1    
      savdir="`pwd`"
      cd $CORE/sentinel
      echo "Execute venv/bin/python bin/sentinel.py"
      echo "Response should be blanks."
      echo "Begin Response."
      venv/bin/python bin/sentinel.py
      echo "End Response."
      cd $savdir
    fi
    echo
}

ShowServerUptime() {
     echo -e "$serverUptime" 
     ## "\n${YELLOW}Server Uptime.${NONE}\n  Using the command:\n   uptime"
     uptime
     echo
}

ShowActiveDaemon() {
    echo -e "$activeDaemon"  
    ## "\n${YELLOW}Check $DAEMON daemon.${NONE}\n\n  The Daemon is a process that communicates to $COIN Network to keep your MasterNode active\n  It must be active to receive rewards\n\n  Using the command:\n   ps -ef | grep $DAEMON\n"
 
    TOT_DAEMON="`ps -ef | grep -c $DAEMON`"


    if [ "$TOT_DAEMON" = "2" ]
    then
      echo -e "$goodDaemon"

    else
       echo -e "$badDaemon"

    fi
   
    echo
}

MasterNodeEnabled() {

    DaemonMustBeActive
    if [ "$TOT_DAEMON" = "2" ]
    then
        echo -e  "$MNENABLED"
           ##"\n${YELLOW}Show MasterNode Enabled Status.${NONE}\n  Using the command:\n   $CLI masternode list full | grep $HOST_IP"

        ##$CLI masternode list full | grep $HOST_IP

        MN_STATUS="`$CLI masternode list full | grep $HOST_IP`"
        echo -e "\n $MN_STATUS   \n"

        echo -e " look for enabled"

        ## I think this can be expressed on command line as
        # xxx-cli masternodelist full `/sbin/ifconfig eth0 | grep Mask | awk '{print $2}'| cut -f2 -d:`


        ## echo $MN_STATUS

        MN_IS_ENABLED=`echo $MN_STATUS | grep ENABLED | wc`

        MN_STATUS=`echo $MN_IS_ENABLED | awk '{print $1}'`

        if [ "$MN_STATUS" = "1" ]
          then
            echo -e  "`date +"%Y-%m-%d %T"` MasterNode $HOST_IP is Up - ${WHITE}ENABLED${NONE}"
          else
            echo -e "`date +"%Y-%m-%d %T"` MasterNode $HOST_IP is Down - ${RED}NOT ENABLED${NONE}"
        fi
    fi
    echo
}

MasternodeListStatus() {
    DaemonMustBeActive
    if [ "$TOT_DAEMON" = "2" ]
    then
        echo -e "$mnList"
        ##echo -e  "\n${YELLOW}Show MasterNode List.${NONE}\n  Using the command:\n   $CLI masternode list addr | grep $HOST_IP"
        ##echo -e "\nYou should find your IP address listed below.  If you do not - your MasterNode is not registered\n\nbegin ...."
        $CLI masternode list addr | grep $HOST_IP
        ##echo "end ....."
    fi
    echo
}

ShowIpAddress() {


IP_ONLY="`echo "$EXTERNAL_IP" | awk -F"=" '{ print $2 }'`"
echo -e "checkingVPS"
   #"${YELLOW}Checking VPS / Masternode external IP address${NONE}\n  Your external ip address for putty / masternode configuration is: $HOST_IP\n"
echo -e "$checkingVPS2"
    #"  To verify everything is setup properly - we scan the config file  [ $CONF ] for the keyword $SCANIP\n  Then we compare the results to the HOST_IP address [ $HOST_IP ]\n"

echo -e  "$checkingVPS3"
  #"\n  To find your IP address we use the command:\n   /sbin/ifconfig eth0 | grep Mask | awk '{print $2}'| cut -f2 -d:\n"
echo -e  "$checkingVPS4"
         #"\n  We compare that to the config file [ $CONF ] to your IP addreess using\n   cat $CONF | grep external\n"

echo -e "$checkingVPS5"
        #"Now we will check VPS configuration file\nThis is located in $CONF"
if [ "$HOST_IP$SCANPORT" != "$IP_ONLY" ]
then
   echo -e "$checkingVPS6"
           #"${RED}** Possibly issue with externalip $IP_ONLY  .... host IP = $HOST_IP $CONF  ${NONE}\n  Use the menu to review your config file"
else
   echo -e "$checkingVPS7"
           #"${WHITE}externalip appears to be configured properly - $EXTERNAL_IP${NONE}"
fi

echo

}

ReviewSyslog() {
  echo -e  "$reviewSyslog"
  ##reviewSyslog="\n${YELLOW}Review System Log for Sentinel issues${NONE}\n  Using the command:\n   grep CRON /var/log/syslog | grep $CORE\n"
  ## reviewSyslog="\n${YELLOW}Revise el registro del sistema para problemas de Sentinel${NONE}\n  Usando el comando:\n   grep CRON /var/log/syslog | grep $CORE\n"

  grep CRON /var/log/syslog | grep $GREPSYSLOG
  echo "`date` CRON Review $GREPSYSLOG log date" >> /var/log/syslog
}

diagnoseCrontab() {
echo -e  "$diagCrontab"
#"\n${YELLOW}crontab diagnosis${NONE}\n  Using the command:\n   crontab -l\n   Use the following command to edit\n   crontab -e\n"

if [ "$MY_CRONTAB" = "$MY_CRONTABREVIEW" ]
  then
     echo -e "$reviewCrontab"
     #"Reviewing crontab for sentinel.\n   $MY_CRONTABREVIEW\n\n${WHITE}Looks good.${NONE}\n"
  else
     echo -e "$reviewError"
     #"${RED}Check crontab - possible error\ndid not find\n$MY_CRONTABREVIEW\n${NONE}"
fi

# reboot

if [ "$MY_CRONTAB2" = "$MY_CRONTABREVIEW2" ]
  then
     echo -e "$reviewCrontab2"
             #"Reviewing crontab for command to restart daemon if VPS is restarted.\n   $MY_CRONTABREVIEW\n\n${WHITE}Looks good.${NONE}\n"
  else
     echo -e "$reviewCrontab3"
     #"${RED}Check crontab - possible error\ndid not find\n$MY_CRONTABREVIEW2\n${NONE}"
fi


}

ShowCrontab() {
   echo -e  "$crontabContents"
   #"\n${YELLOW}crontab contents${NONE}\n  Using the command:\n   crontab -l\n   Use crontab -e to edit\n"
   crontab -l
}

ShowWalletInfo() {
    DaemonMustBeActive
    if [ "$TOT_DAEMON" = "2" ]
    then
        echo -e "$walletDaemon"
        ##echo -e  "\n${YELLOW}The deamon is active so we show the wallet information${NONE}\n  Using the command:\n   $CLI getinfo;\n"
        $CLI getinfo;
    fi
}

ShowWalletSyncStatus() {
    DaemonMustBeActive
    if [ "$TOT_DAEMON" = "2" ]
    then
       echo -e "$walletSync" 
       ##  "\n${YELLOW}The deamon is active so we show the wallet information${NONE}\n  Using the command:\n   $CLI mnsync status\n"
       SYNCSTATUS="`$CLI mnsync status`"
       SYNCSTATUSRESULT="`$CLI mnsync status | grep IsMasternodeListSynced | awk '{print $2}'`"

       echo "$SYNCSTATUS"

       if [ "$SYNCSTATUSRESULT" = "true," ]
       then
          echo -e "$walletSync2"
          ##"${WHITE}The wallet is synced.${NONE}\n"
       else
          echo -e "$walletSync3"
          ##"${RED}The wallet has not finished syncing.${NONE}\n"
       fi
    fi
}

MasternodeStatus() {
    DaemonMustBeActive
    if [ "$TOT_DAEMON" = "2" ]
    then
        echo -e "$mnStatus"
        ## "\n${YELLOW}The deamon is active so we show the wallet information${NONE}\n  Using the command:\n   $CLI masternode status\n"
        $CLI masternode status
        echo
    fi
}

ReindexWallet() {
    echo '** not ready yet ** Shutting down wallet daemon...'
}

DisplayWalletConfig() {
    echo -e  "$walletConfig"
    #"\n${YELLOW}Display Wallet daemon${NONE}\n  Using the command:\n          cat $CONF\n"
    cat $CONF
    echo
}

EditWalletConfig() {
    sudo nano $CONF
}

ViewSentinelLogFile() {
    echo -e  "$logFile"
    #"\n${YELLOW}View Sentinel Log File${NONE}\n  Using the command:\n          tail $SENTINEL_LOG\n"
    tail $SENTINEL_LOG
}

ClearSentinelLogFile() {
    echo -e  "$clearLogFile"
    #"\n${YELLOW}Clear Sentinel Log File${NONE}\n  Using the command:\n          echo \" \" > $SENTINEL_LOG\n"
    echo "" >  $SENTINEL_LOG
}

DateSentinelLogFile() {
    echo -e  "$dateLogFile"
    #"\n${YELLOW}Add a Date to Sentinel Log File${NONE}\n  Using the command:\n          date \" \" >> $SENTINEL_LOG\n"
    date >>  $SENTINEL_LOG
}

StartWalletDaemon() {
    echo -e  "$startWalletDaemon"
    #"\n${YELLOW}Start Wallet daemon${NONE}\n  Using the command:\n          $DAEMON -daemon\n"

    TOT_DAEMON="`ps -ef | grep -c $DAEMON`"

    if [ "$TOT_DAEMON" = "2" ]
    then
       echo -e "$daemonRunning"
           #"${RED}** The $DAEMON is already running [ ps -ef | grep -c $DAEMON ] ${NONE}"
    else
       echo "Starting $DAEMON"
       $DAEMON -daemon
       sleep 5

    fi

    echo
}

StopWalletDaemon() {
    echo -e  "$stopWallet"
             #"\n${YELLOW}Stop Wallet daemon${NONE}\n  Using the command:\n          $CLI stop\n"
    DaemonMustBeActive
    if [ "$TOT_DAEMON" = "2" ]
    then
      $CLI stop
      sleep 5

    fi
}

killWalletDaemon() {
    echo -e  "$killWallet"
             #"\n${YELLOW}Stop Wallet daemon${NONE}\n  Using the command:\n          ps -aef | grep $DAEMON | grep -v grep | awk '{print $2}' | xargs kill\n"
    DaemonMustBeActive

    if [ "$TOT_DAEMON" = "2" ]
    then
       ps -aef | grep $DAEMON | grep -v grep | awk '{print $2}' | xargs kill
       echo "$killWallet2"
            #"If there were no errors - the process has been killed"
    else
       echo -e "${YELLOW}** $DAEMON wallet daemon is not running - no need to stop${NONE}"

    fi

}


ReviewMyMasterNode() {
    ShowOSversion
    ShowServerUptime
    ShowFreeSpace
    ShowFirewall 
    ShowIpAddress
    diagnoseCrontab
    ShowWalletInfo
    ShowWalletSyncStatus
    ShowActiveDaemon
    MasterNodeEnabled
}

ShowFreeSpace() {
    echo -e "$freeSpace"
    ## "\n${YELLOW}free space / swap${NONE}\n  Using the command:\n   free -h\n"
    
    echo -e  "\n$freeSpace1 ${YELLOW} `free -h | grep Mem: | awk '{print $2}'` ${NONE} $freeSpace2"
    echo -e "$freeSpace3"
    #"It appears that you currently have ${YELLOW} `free -h | grep Swap | awk '{print $2}'` ${NONE} swap space available\n"
    free -h
    echo
}

ShowOSversion() {
    echo -e "$operatingSystem"
    ##  "\n${YELLOW}Operating system version${NONE}\n  Using the command:\n    cat /etc/issue.net\n"
    cat /etc/issue.net
    echo
}

ShowWalletMenu() {
  response1="1"
  declare -ga WalletMenu=("" "${CYAN}[1]${NONE} Show Active wallet daemon" "${CYAN}[2]${NONE} Show wallet info"
    "${CYAN}[3]${NONE} Show wallet sync status" "${CYAN}[4]${NONE} Start wallet daemon"
    "${CYAN}[5]${NONE} Stop wallet daemon" "${CYAN}[6]${NONE} Display wallet config"
    "${CYAN}[7]${NONE} Edit wallet config")
  declare -ga WalletMenuOptions=("" "ShowActiveDaemon" "ShowWalletInfo" "ShowWalletSyncStatus" "StartWalletDaemon" "StopWalletDaemon" "DisplayWalletConfig" "EditWalletConfig")

if [ "$LANGUAGE" = "LANGUAGE=SPANISH" ]
    then
    declare -ga WalletMenu=("" "${CYAN}[1]${NONE} Mostrar daemon de monedero activo" "${CYAN}[2]${NONE} Mostrar información de la billetera"
    "${CYAN}[3]${NONE} Mostrar el estado de sincronización de la billetera" "${CYAN}[4]${NONE} Inicie el daemon de monedero"
    "${CYAN}[5]${NONE} Detener wallet daemon" "${CYAN}[6]${NONE} Mostrar configuración de billetera"
    "${CYAN}[7]${NONE} Editar la configuración de la billetera")

fi

if [ "$LANGUAGE" = "LANGUAGE=RUSSIAN" ]
    then
  declare -ga WalletMenu=("" "${CYAN}[1]${NONE} Показать активный кошелек daemon" "${CYAN}[2]${NONE} Показать информацию о кошельке"
    "${CYAN}[3]${NONE} Показывать статус синхронизации кошелька" "${CYAN}[4]${NONE} Начало демона кошелька"
    "${CYAN}[5]${NONE} Демон дескриптора кошелька" "${CYAN}[6]${NONE} Показать конфигурацию кошелька"
    "${CYAN}[7]${NONE} Изменить конфигурацию кошелька")
fi

  until [ "$response1" == "" ]; do
  zi2=0
    for i in "${WalletMenu[@]}"
    do

      if [ "$CMDLINE" == "/help" ]
      then
        chrlen=${#WalletMenuOptions[$zi2]}
        if [ "$chrlen" -gt "14" ]
          then
          tab="\t"
        else
          tab="\t\t"
        fi
        echo -e "\t${WalletMenuOptions[$zi2]} $tab $i"
        response1=""
      else
          echo -e "$i"
      fi
      let "zi2 = $zi2 + 1"

    done

    if [ "$CMDLINE" != "/help" ]
      then
        echo -e "${BOLD}$whatWouldYou${NONE}"
        read -p "$selectDescription " response1
        echo
    fi

    enterToContinue="$response1"

    if [ "$CMDLINE" != "/help" ]
      then
        until [ "$enterToContinue" == "" ]; do
        ${WalletMenuOptions[enterToContinue]}
        read -p "$pressEnter " enterToContinue
      done
    fi

  done

}

ShowMasterNodeMenu() {
  declare -ga MasterNodeMenu=("" "${CYAN}[1]${NONE} Show Masternode List Status" "${CYAN}[2]${NONE} Show Masternode Status"
                        "${CYAN}[3]${NONE} Is MasterNode Enabled"
                        "${CYAN}[4]${NONE} Show IP Address / Config File validation" "${CYAN}[5]${NONE} Show Sentinel")
if [ "$LANGUAGE" = "LANGUAGE=SPANISH" ]
    then
    declare -ga MasterNodeMenu=("" "${CYAN}[1]${NONE} Mostrar estado de la lista Masternode" "${CYAN}[2]${NONE} Mostrar estado de Masternode"
                        "${CYAN}[3]${NONE} ¿MasterNode está habilitado?"
                        "${CYAN}[4]${NONE} Mostrar la validación de la dirección IP / archivo de configuración" "${CYAN}[5]${NONE} Mostrar Sentinel")
fi

if [ "$LANGUAGE" = "LANGUAGE=RUSSIAN" ]
    then
  declare -ga MasterNodeMenu=("" "${CYAN}[1]${NONE} Показать статус списка Masternode" "${CYAN}[2]${NONE} Показать статус Masternode"
                        "${CYAN}[3]${NONE} Включен ли MasterNode"
                        "${CYAN}[4]${NONE} Показать подтверждение IP-адреса / конфига" "${CYAN}[5]${NONE} Показать Sentinel")
fi

  declare -ga MasterNodeMenuOptions=("" "MasternodeListStatus" "MasternodeStatus" "MasterNodeEnabled" "ShowIpAddress" "ShowSentinel")

  response1="1"

  until [ "$response1" == "" ]; do
    zi2=0
    for i in "${MasterNodeMenu[@]}"
    do
      if [ "$CMDLINE" == "/help" ]
      then
        chrlen=${#MasterNodeMenuOptions[$zi2]}
        if [ "$chrlen" -gt "14" ]
          then
          tab="\t"
        else
          tab="\t\t"
        fi
        echo -e "\t${MasterNodeMenuOptions[$zi2]} $tab $i"
        response1=""
      else
          echo -e "$i"
      fi
      let "zi2 = $zi2 + 1"

    done

    if [ "$CMDLINE" != "/help" ]
      then
        echo -e "${BOLD}$whatWouldYou${NONE}"
        read -p "$selectDescription " response1
        echo
    fi


    enterToContinue="$response1"

    if [ "$CMDLINE" != "/help" ]
      then
        until [ "$enterToContinue" == "" ]; do
        ${MasterNodeMenuOptions[enterToContinue]}
        read -p "$pressEnter " enterToContinue
      done
    fi

  done


}

ShowSentinelMenu() {
response1="1"

declare -ga SentinelMenu=("" "${CYAN}[1]${NONE} View sentinel log file" "${CYAN}[2]${NONE} Clear sentinel log file"
                         "${CYAN}[3]${NONE} Add a time date stamp to sentinel log file" "${CYAN}[4]${NONE} Show Sentinel" "${CYAN}[5]${NONE} Review System Log for Sentinel Issues")
if [ "$LANGUAGE" = "LANGUAGE=SPANISH" ]
    then
    declare -ga SentinelMenu=("" "${CYAN}[1]${NONE} Ver archivo de registro centinela" "${CYAN}[2]${NONE} Borrar el archivo de registro centinela"
                         "${CYAN}[3]${NONE} Agregue un sello de fecha y hora al archivo de registro centinela" "${CYAN}[4]${NONE} Mostrar Sentinel" "${CYAN}[5]${NONE} Revise el registro del sistema para problemas de Sentinel")
fi
if [ "$LANGUAGE" = "LANGUAGE=RUSSIAN" ]
    then
    declare -ga SentinelMenu=("" "${CYAN}[1]${NONE} Просмотр файла журнала отправителя" "${CYAN}[2]${NONE} Очистить файл журнала дозорного"
                         "${CYAN}[3]${NONE} Добавить метку даты в файл журнала дозорного" "${CYAN}[4]${NONE} Показать Sentinel" "${CYAN}[5]${NONE} Обзор системного журнала для проблем Sentinel")
fi
declare -ga SentinelMenuOptions=("" "ViewSentinelLogFile" "ClearSentinelLogFile" "DateSentinelLogFile" "ShowSentinel" "ReviewSyslog")

  until [ "$response1" == "" ]; do
    zi2=0
    for i in "${SentinelMenu[@]}"
    do
      if [ "$CMDLINE" == "/help" ]
      then
        chrlen=${#SentinelMenuOptions[$zi2]}
        if [ "$chrlen" -gt "14" ]
          then
          tab="\t"
        else
          tab="\t\t"
        fi
        echo -e "\t${SentinelMenuOptions[$zi2]} $tab $i"
        response1=""
      else
          echo -e "$i"
      fi
      let "zi2 = $zi2 + 1"

    done

    if [ "$CMDLINE" != "/help" ]
      then
        echo -e "${BOLD}$whatWouldYou${NONE}"
        read -p "$selectDescription " response1
        echo
    fi

    enterToContinue="$response1"

    if [ "$CMDLINE" != "/help" ]
      then
        until [ "$enterToContinue" == "" ]; do
        ${SentinelMenuOptions[enterToContinue]}
        read -p "$pressEnter " enterToContinue
      done
    fi

  done


}

LanguageMenu() {

declare -ga LanguageMenu=("" "${CYAN}[1]${NONE} Set Menu to English" "${CYAN}[2]${NONE} Set Menu to Spanish" "${CYAN}[3]${NONE} Set Menu Russian")
if [ "$LANGUAGE" = "LANGUAGE=SPANISH" ]
    then
    declare -ga LanguageMenu=("" "${CYAN}[1]${NONE} Establecer menú en inglés" "${CYAN}[2]${NONE} Establecer menú para español" "${CYAN}[3]${NONE} Establecer el menú ruso")
fi
if [ "$LANGUAGE" = "LANGUAGE=RUSSIAN" ]
    then
    declare -ga LanguageMenu=("" "${CYAN}[1]${NONE} Установить меню на английский язык" "${CYAN}[2]${NONE} Установить меню на испанский" "${CYAN}[3]${NONE} Меню Set Russian")
fi

declare -ga LanguageMenuOptions=("" "SetLanguage EENGLISH" "SetLanguage SPANISH" "SetLanguage RUSSIAN")

response1="1"

  until [ "$response1" == "" ]; do
    zi2=0
    for i in "${LanguageMenu[@]}"
    do
      if [ "$CMDLINE" == "/help" ]
      then
        chrlen=${#LanguageMenuOptions[$zi2]}
        if [ "$chrlen" -gt "14" ]
          then
          tab="\t"
        else
          tab="\t\t"
        fi
        echo -e "\t${LanguageMenuOptions[$zi2]} $tab $i"
        response1=""
      else
          echo -e "$i"
      fi
      let "zi2 = $zi2 + 1"

    done

    if [ "$CMDLINE" != "/help" ]
      then
        echo -e "${BOLD}$whatWouldYou${NONE}"
        read -p "$selectDescription " response1
        echo
    fi

    enterToContinue="$response1"

    if [ "$CMDLINE" != "/help" ]
      then
        until [ "$enterToContinue" == "" ]; do
        ${LanguageMenuOptions[enterToContinue]}
        read -p "$pressEnter " enterToContinue
      done
    fi

  done
}

SetLanguage() {
  confRemove "LANGUAGE="
  echo "LANGUAGE=$1" >> $CORE/$TOOLNAME.conf
  LANGUAGE="`cat $CORE/$TOOLNAME.conf`"
  exit
}

confRemove() {
  oldConf=`cat $CORE/$TOOLNAME.conf | grep -v $1`
  echo "$oldConf" > $CORE/$TOOLNAME.conf
}


downLoadMenu() {

declare -ga DownLoadMenu=("" "${CYAN}[1]${NONE} Set Location to Download to production (Stable Releases)" "${CYAN}[2]${NONE} Set Location to Download to masternodetools.com (newest / not the coin developer site / tool developer site)")
if [ "$LANGUAGE" = "LANGUAGE=SPANISH" ]
    then
    declare -ga DownLoadMenu=("" "${CYAN}[1]${NONE} Establecer la ubicación para descargar a producción (Versiones estables)" "${CYAN}[2]${NONE} Establezca la ubicación para descargar a masternodetools.com (el sitio del desarrollador del sitio / herramienta desarrollador de monedas más nuevo / no)")
fi
if [ "$LANGUAGE" = "LANGUAGE=RUSSIAN" ]
    then
    declare -ga DownLoadMenu=("" "${CYAN}[1]${NONE} Установить местоположение для загрузки в производство (стабильные выпуски)" "${CYAN}[2]${NONE} Установить местоположение для загрузки на masternodetools.com (новейший / не сайт разработчика сайта / разработчика монеты)")
fi

declare -ga DownLoadMenuOptions=("" "SetDownload github" "SetDownload toolsite")

responseD1="1"

  until [ "$responseD1" == "" ]; do
    zi2=0
    for i in "${DownLoadMenu[@]}"
    do
      if [ "$CMDLINE" == "/help" ]
      then
        chrlen=${#DownLoadMenuOptions[$zi2]}
        if [ "$chrlen" -gt "14" ]
          then
          tab="\t"
        else
          tab="\t\t"
        fi
        echo -e "\t${DownLoadMenuOptions[$zi2]} $tab $i"
        responseD1=""
      else
          echo -e "$i"
      fi
      let "zi2 = $zi2 + 1"
    done

    if [ "$CMDLINE" != "/help" ]
      then
        echo -e "${BOLD}$whatWouldYou${NONE}"
        read -p "$selectDescription " responseD1
        echo
    fi

    enterToContinue="$responseD1"

    if [ "$CMDLINE" != "/help" ]
      then
        until [ "$enterToContinue" == "" ]; do
        ${DownLoadMenuOptions[enterToContinue]}
        read -p "$pressEnter " enterToContinue
      done
    fi

  done
}

downloadLocation() {
  TOOLDOWNLOAD="`cat $CORE/$TOOLNAME.conf | grep TOOLDOWNLOAD | awk -F\= '{gsub(/"/,"",$2);print $2}'`"

  if [ "$TOOLDOWNLOAD" = "" ]
    then  
      # No setting in config file
      TOOLHUB="https://github.com/sucremoneda/sucrtools"
  elif [ "$TOOLDOWNLOAD" = "github" ]
    then
      # github / coin website
      TOOLHUB="https://github.com/sucremoneda/sucrtools"
  else
      # Traditional location
      TOOLHUB=masternodetools.com/tools
  fi
}

SetDownload() {
  confRemove "TOOLDOWNLOAD="
  echo "TOOLDOWNLOAD=$1" >> $CORE/$TOOLNAME.conf
}

counter () {

  ## Total useage
  TOTALCTR="`cat $CORE/$TOOLNAME.conf | grep -m1 \TOTALCTR | awk '{print $1}'| cut -f2 -d:`"
  let "TOTALCTR = $TOTALCTR + 1"
  confRemove "TOTALCTR="
  echo "TOTALCTR=$TOTALCTR" >> $CORE/$TOOLNAME.conf

  ## Count to 10 check for update
  USEAGE="`cat $CORE/$TOOLNAME.conf | grep -m1 \USEAGE | awk '{print $1}'| cut -f2 -d:`"
  let "USEAGE = $USEAGE + 1"
  confRemove "USEAGE="
  let "testUSEAGE = $USEAGE"

 if [ "$USEAGE" -gt 10 ]
  then
    downloadLocation
    wget $TOOLHUB/$TOOLNAME -O $TOOLTMP/$TOOLNAMENEW  > /dev/null 2>&1
    if [ $? -ne 0 ]
      then
      do_nothing="Y"
    else

        NEWVERSION="`cat $TOOLTMP/$TOOLNAMENEW | grep -m1 \#version: | awk '{print $1}'| cut -f2 -d:`"

        if [ "$CURVERSION" = "$NEWVERSION" ]
        then
            do_nothing="Y"
        else

           echo -e "\n${RED}** Upgrade available to version $NEWVERSION ** ${NONE}\n"
           CHANGES="`cat $TOOLTMP/$TOOLNAMENEW | grep -m1 \#changelog:`"
           echo -e "${YELLOW}$CHANGES${NONE}\n"

        fi
    fi
    USEAGE="0"
 fi
  echo "USEAGE=$USEAGE" >> $CORE/$TOOLNAME.conf
}


ShowSystemMenu() {

declare -ga SystemMenu=("" "${CYAN}[1]${NONE} Show Firewall" "${CYAN}[2]${NONE} Show crontab (Scheduled tasks)"
                          "${CYAN}[3]${NONE} Diagnose crontab (Scheduled tasks)" "${CYAN}[4]${NONE} Show Server Uptime"
                          "${CYAN}[5]${NONE} Show IP Address" "${CYAN}[6]${NONE} Show Free Space"
                          "${CYAN}[7]${NONE} Show OS version" "${CYAN}[8]${NONE} Language Options" "${CYAN}[9]${NONE} Download Updates Location")
if [ "$LANGUAGE" = "LANGUAGE=SPANISH" ]
    then
declare -ga SystemMenu=("" "${CYAN}[1]${NONE} Mostrar Firewall" "${CYAN}[2]${NONE} Mostrar crontab (tareas programadas)"
                          "${CYAN}[3]${NONE} Diagnosticar crontab (Tareas programadas)" "${CYAN}[4]${NONE} Mostrar el tiempo de actividad del servidor"
                          "${CYAN}[5]${NONE} Mostrar dirección IP" "${CYAN}[6]${NONE} Mostrar espacio libre"
                          "${CYAN}[7]${NONE} Mostrar la versión del sistema operativo" "${CYAN}[8]${NONE} Opciones de lenguaje"  "${CYAN}[9]${NONE} Descargue Actualizaciones Ubicación" )
fi

if [ "$LANGUAGE" = "LANGUAGE=RUSSIAN" ]
    then
declare -ga SystemMenu=("" "${CYAN}[1]${NONE} Показать межсетевой экран" "${CYAN}[2]${NONE} Показать crontab (Запланированные задания)"
                          "${CYAN}[3]${NONE} Диагностика crontab (Запланированные задания)" "${CYAN}[4]${NONE} Показывать время сервера"
                          "${CYAN}[5]${NONE} Показать IP-адрес" "${CYAN}[6]${NONE} Показать свободное пространство"
                          "${CYAN}[7]${NONE} Показать версию ОС" "${CYAN}[8]${NONE} Параметры языка" "${CYAN}[9]${NONE} Загрузка обновлений")
fi

declare -ga SystemMenuOptions=("" "ShowFirewall" "ShowCrontab" "diagnoseCrontab" "ShowServerUptime"
                                 "ShowIpAddress" "ShowFreeSpace" "ShowOSversion" "LanguageMenu" "downLoadMenu")
response1="1"

  until [ "$response1" == "" ]; do
    zi2=0
    for i in "${SystemMenu[@]}"
    do
      if [ "$CMDLINE" == "/help" ]
      then
        chrlen=${#SystemMenuOptions[$zi2]}
        if [ "$chrlen" -gt "14" ]
          then
          tab="\t"
        else
          tab="\t\t"
        fi
        echo -e "\t${SystemMenuOptions[$zi2]} $tab $i"
        response1=""
      else
          echo -e "$i"
      fi
      let "zi2 = $zi2 + 1"

    done

    if [ "$CMDLINE" != "/help" ]
      then
        echo -e "${BOLD}$whatWouldYou${NONE}"
        read -p "$selectDescription " response1
        echo
    fi

    enterToContinue="$response1"

    if [ "$CMDLINE" != "/help" ]
      then
        until [ "$enterToContinue" == "" ]; do
        ${SystemMenuOptions[enterToContinue]}
        read -p "$pressEnter " enterToContinue
      done
    fi

  done


}

ShowAbout() {
    echo -e "$aboutInfo"
    echo
    read -p "$pressEnter1 " enterToContinue

}




Upgrade() {

    echo -e "\n${WHITE}Check for upgrade.${NONE}"
    downloadLocation
    wget $TOOLHUB/$TOOLNAME -O $TOOLTMP/$TOOLNAMENEW  > /dev/null 2>&1
    if [ $? -ne 0 ]
      then
      echo -e "${RED}Not able to reach server $TOOLHUB/$TOOLNAME ${NONE}"
      echo -e "Try again later.\n"
    else

        NEWVERSION="`cat $TOOLTMP/$TOOLNAMENEW | grep -m1 \#version: | awk '{print $1}'| cut -f2 -d:`"

        if [ "$CURVERSION" = "$NEWVERSION" ]
        then
            echo "$currentRelease"
            #"Already at current or newest release $CURVERSION"
            echo -e "${WHITE}Press Enter to continue.${NONE}"
            read -e -p "" $continue
        else
            CHANGES="`cat $TOOLTMP/$TOOLNAMENEW | grep -m1 \#changelog:`"
            echo -e "$currentRelease2"
               #"An Upgrade from $CURVERSION to $NEWVERSION is available - it includes the following changes ${GREEN}\n$CHANGES${NONE}"
            read -e -p "Upgrade to new version of MasterNode Utilities? [Y/n] :" upgrade_now
            if [[ ("$upgrade_now" == "y" || "$upgrade_now" == "Y" || "$upgrade_now" == "") ]]; then
                echo "$currentRelease3"
                   #"Let's upgrade - restart tools after upgrade"
                chmod 755 $TOOLTMP/$TOOLNAMENEW
                mv $TOOLTMP/$TOOLNAMENEW $TOOLBIN/$TOOLNAME
                exit

            else
                echo -e "$currentRelease4"
                #"Upgrade cancelled."
            fi
        fi
    fi

}

DaemonMustBeActive() {
   echo -e  "$daemonMustBeRunning1"
   ##"\n${YELLOW}The daemon must be active to perform this command${NONE}\n  Using the command:\n   ps -ef | grep -c $DAEMON\n   we check to see if the daemon is running\n"

    TOT_DAEMON="`ps -ef | grep -c $DAEMON`"


    if [ "$TOT_DAEMON" = "2" ]
    then
    stop="Yes"
    else
       echo -e "$daemonMustBeRunning2"
       ##"${YELLOW}The daemon $DAEMON must be running to use option\nUse Start Wallet Daemon option${NONE}"
    fi
}

#######################################
counter

HOST_IP="`/sbin/ifconfig eth0 | grep Mask | awk '{print $2}'| cut -f2 -d:`"
SCANIP="external"
EXTERNAL_IP="`cat $CONF | grep $SCANIP`"
IP_ONLY="`echo "$EXTERNAL_IP" | awk -F"=" '{ print $2 }'`"
MY_CRONTAB="`crontab -l | grep sucrcore | grep sentinel-cron.log`"
MY_CRONTABREVIEW="* * * * * cd /root/.sucrcore/sentinel && ./venv/bin/python bin/sentinel.py >> sentinel-cron.log 2>&1"
MY_CRONTAB2="`crontab -l | grep $DAEMON`"
MY_CRONTABREVIEW2="@reboot sucrd -daemon"

declare -ga MainMenu=("" "${CYAN}[1]${NONE} Review My MasterNode" "${CYAN}[2]${NONE} Show Wallet Menu"
                "${CYAN}[3]${NONE} Show Sentinel Menu" "${CYAN}[4]${NONE} Masternode Menu"
                "${CYAN}[5]${NONE} Show System Menu" "" ""
                "${CYAN}[8]${NONE} Show About / Information" "${CYAN}[9]${NONE} Check for new version / upgrade this script")
menuDescription="+-ver: $CURVERSION ----------------------+\n"
menuDescription+="| ${CYAN}$COIN Coin MasterNode Utilities${NONE}  |\n"
menuDescription+="+----------------------------------+\n"


whatWouldYou="What would you like me to do?"
selectDescription="Select option with number:"
pressEnter1="Press enter to continue"
pressEnter="Press enter or Option to continue"
walletDaemon="\n${YELLOW}The deamon is active so we show the wallet information${NONE}\n  Using the command:\n   $CLI getinfo;\n"
walletSync="\n${YELLOW}The deamon is active so we show the wallet information${NONE}\n  Using the command:\n   $CLI mnsync status\n"
walletSync2="${WHITE}The wallet is synced.${NONE}\n"
walletSync3="${RED}The wallet has not finished syncing.${NONE}\n"
daemonMustBeRunning1=#"\n${YELLOW}The daemon must be active to perform this command${NONE}\n  Using the command:\n   ps -ef | grep -c $DAEMON\n   we check to see if the daemon is running\n"
daemonMustBeRunning2="${YELLOW}The daemon $DAEMON must be running to use option\nUse Start Wallet Daemon option${NONE}"
operatingSystem="\n${YELLOW}Operating system version${NONE}\n  Using the command:\n    cat /etc/issue.net\n"
serverUptime="\n${YELLOW}Server Uptime.${NONE}\n  Using the command:\n   uptime"
activeDaemon="\n${YELLOW}Check $DAEMON daemon.${NONE}\n\n  The Daemon is a process that communicates to $COIN Network to keep your MasterNode active\n  It must be active to receive rewards\n\n  Using the command:\n   ps -ef | grep $DAEMON\n"
goodDaemon="${WHITE}Proper number of $DAEMON daemons running${NONE}"
badDaemon="${RED}[Error - Invalid number of $DAEMON daemons running${NONE}"
mnList="\n${YELLOW}Show MasterNode List.${NONE}\n  Using the command:\n   $CLI masternode list addr | grep $HOST_IP\nYou should find your IP address listed below.  If you do not - your MasterNode is not registered\n\nbegin ...."
mnStatus="\n${YELLOW}The deamon is active so we show the wallet information${NONE}\n  Using the command:\n   $CLI masternode status\n"
freeSpace="\n${YELLOW}free space / swap${NONE}\n  Using the command:\n   free -h\n"
freeSpace1="Your VPS is configured with"
freeSpace2="memory available"
freeSpace3="It appears that you currently have ${YELLOW} `free -h | grep Swap | awk '{print $2}'` ${NONE} swap space available\n"
walletConfig="\n${YELLOW}Display Wallet daemon${NONE}\n  Using the command:\n          cat $CONF\n"
logFile="\n${YELLOW}View Sentinel Log File${NONE}\n  Using the command:\n          tail $SENTINEL_LOG\n"
clearLogFile="\n${YELLOW}Clear Sentinel Log File${NONE}\n  Using the command:\n          echo \" \" > $SENTINEL_LOG\n"
dateLogFile="\n${YELLOW}Add a Date to Sentinel Log File${NONE}\n  Using the command:\n          date \" \" >> $SENTINEL_LOG\n"
startWalletDaemon="\n${YELLOW}Start Wallet daemon${NONE}\n  Using the command:\n          $DAEMON -daemon\n"
firewall="\n${YELLOW}Display firewall rules${NONE}\n  Using the command:\n   ufw status numbered\n"
diagCrontab="\n${YELLOW}crontab diagnosis${NONE}\n  Using the command:\n   crontab -l\n   Use the following command to edit\n   crontab -e\n"
reviewCrontab="Reviewing crontab for sentinel.\n   $MY_CRONTABREVIEW\n\n${WHITE}Looks good.${NONE}\n"
reviewSyslog="\n${YELLOW}Review System Log for Sentinel issues${NONE}\n  Using the command:\n   grep CRON /var/log/syslog | grep $GREPSYSLOG\n"
reviewCrontab2="Reviewing crontab for command to restart daemon if VPS is restarted.\n   $MY_CRONTABREVIEW2\n\n${WHITE}Looks good.${NONE}\n"
reviewCrontab3="${RED}Check crontab - possible error\ndid not find\n$MY_CRONTABREVIEW2\n${NONE}"
reviewError="${RED}Check crontab - possible error\ndid not find\n$MY_CRONTABREVIEW\n${NONE}"
crontabContents="\n${YELLOW}crontab contents${NONE}\n  Using the command:\n   crontab -l\n   Use crontab -e to edit\n"
checkingVPS="${YELLOW}Checking VPS / Masternode external IP address${NONE}\n  Your external ip address for putty / masternode configuration is: $HOST_IP\n"
checkingVPS2="  To verify everything is setup properly - we scan the config file  [ $CONF ] for the keyword $SCANIP\n  Then we compare the results to the HOST_IP address [ $HOST_IP ]\n"
checkingVPS3="\n  To find your IP address we use the command:\n   /sbin/ifconfig eth0 | grep Mask | awk '{print $2}'| cut -f2 -d:\n"
checkingVPS4="\n  Compare that information to the config file [ $CONF ] to your IP address Using the command:\n   cat $CONF | grep external\n"
checkingVPS5="Now we will check VPS configuration file\nThis is located in $CONF"
checkingVPS6="${RED}** There maybe an issue with externalip $IP_ONLY  .... host IP = $HOST_IP $CONF  ${NONE}\n  Use the menu to review your config file"
checkingVPS7="${WHITE}externalip appears to be configured properly - $EXTERNAL_IP${NONE}"
stopWallet="\n${YELLOW}Stop Wallet daemon${NONE}\n  Using the command:\n          $CLI stop\n"
killWallet="\n${YELLOW}Stop Wallet daemon${NONE}\n  Using the command:\n          ps -aef | grep $DAEMON | grep -v grep | awk '{print $2}' | xargs kill\n"
killWallet2="If there were no errors - the process has been killed"
daemonRunning="${RED}** The $DAEMON is already running [ ps -ef | grep -c $DAEMON ] ${NONE}"
helpOptions="${YELLOW}Display $TOOLNAME help options\n${WHITE} Use command:\n  $TOOLNAME {argument}${NONE}\n"
aboutInfo="${WHITE}+----------------------------------------------------+${NONE}\n"
aboutInfo+="${WHITE}| ${CYAN}$COIN Masternode Tools ver: $CURVERSION                  ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE}                                                    ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} ${YELLOW}The tools are free of charge.                      ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE}                                                    ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} ${YELLOW}Consider using the donation address if you want    ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} ${YELLOW}us to continue the development of these tools      ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE}                                                    ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} SAROS Donation: SeNPFMFBAPvSV2FFR8Tc2m6dncTE2ky7Se ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} SUCR  Donation: SjUp3Fu2eB7wLBAQkFHs946m1ScWjWXbdr ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} GCH   Donation: GT8bbHhtEQSzgMoXvLeHTf7dL9HwuGVeGY ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} LILI  Donation: GT8bbHhtEQSzgMoXvLeHTf7dL9HwuGVeGY ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE}                                                    ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}+----------------------------------------------------+${NONE}\n"
aboutInfo+="\nTools have been used $TOTALCTR times.\n"
MNENABLED="\n${YELLOW}Show MasterNode Enabled Status.${NONE}\n  Using the command:\n   $CLI masternode list full | grep $HOST_IP"

currentRelease="Already at current or newest release $CURVERSION"
currentRelease2="An Upgrade from $CURVERSION to $NEWVERSION is available - it includes the following changes ${GREEN}\n$CHANGES${NONE}"
currentRelease3="Let's upgrade - restart tools after upgrade"
currentRelease4="Upgrade cancelled."


if [ "$LANGUAGE" = "LANGUAGE=SPANISH" ]
    then

  declare -ga MainMenu=("" "${CYAN}[1]${NONE} Revisa mi MasterNode" "${CYAN}[2]${NONE} Mostrar el menú de Wallet"
                "${CYAN}[3]${NONE} Mostrar menú de Sentinel" "${CYAN}[4]${NONE} Menú Masternode"
                "${CYAN}[5]${NONE} Mostrar menú del sistema" "" ""
                "${CYAN}[8]${NONE} Mostrar información / información" "${CYAN}[9]${NONE} Compruebe si hay una nueva versión / actualice esta secuencia de comandos")
  #menuDescription="utilidades de masternode de monedas"
  menuDescription="+-ver: $CURVERSION --------------------------------+\n"
  menuDescription+="| ${CYAN}$COIN utilidades de masternode de monedas${NONE}  |\n"
  menuDescription+="+--------------------------------------------+\n"
  whatWouldYou="¿Que querrías que hiciera?"
  selectDescription="Seleccione la opción con número:"
  pressEnter1="Presione enter para continuar"
  pressEnter="Presione enter o Option para continuar"
  walletDaemon="\n${YELLOW}El Deamon está activo, así que mostramos la información de la billetera${NONE}\n Usando el comando:\n   $CLI getinfo;\n"
  walletSync="\n${YELLOW}El Deamon está activo, así que mostramos la información de la billetera${NONE}\n  Usando el comando:\n   $CLI mnsync status\n"
  walletSync2="${WHITE}La billetera está sincronizada.${NONE}\n"
  walletSync3="${RED}La billetera no ha terminado de sincronizarse.${NONE}\n"
  daemonMustBeRunning1=#"\n${YELLOW}El daemon debe estar activo para realizar este comando${NONE}\n  Usando el comando:\n   ps -ef | grep -c $DAEMON\n   verificamos si el daemon se está ejecutando\n"
  daemonMustBeRunning2="${YELLOW}El daemon $DAEMON debe estar ejecutándose para usar la opción\nUse la opción Start Wallet Daemon${NONE}"
  operatingSystem="\n${YELLOW}Versión del sistema operativo${NONE}\n  Usando el comando:\n    cat /etc/issue.net\n"
  serverUptime="\n${YELLOW}Tiempo de actividad del servidor.${NONE}\n  Usando el comando:\n   uptime"
  activeDaemon="\n${YELLOW}Comprobar $DAEMON demonio.${NONE}\n\n  El Daemon es un proceso que se comunica con $ COIN Network para mantener su MasterNode activo\n  Debe estar activo para recibir recompensas\n\n  Usando el comando:\n   ps -ef | grep $DAEMON\n"
  goodDaemon="${WHITE}Número correcto de daemons $DAEMON en ejecución${NONE}"
  badDaemon="${RED}Error: cantidad no válida de daemons $DAEMON en ejecución${NONE}"
  mnList="\n${YELLOW}Mostrar la lista MasterNode..${NONE}\n  Usando el comando:\n   $CLI masternode list addr | grep $HOST_IP\nDebería encontrar su dirección IP listada a continuación. Si no lo hace, su MasterNode no está registrado\n\nempezar ...."
  mnStatus="\n${YELLOW}El Deamon está activo, así que mostramos la información de la billetera${NONE}\n  Usando el comando:\n   $CLI masternode status\n"
  freeSpace="\n${YELLOW}espacio libre / swap${NONE}\n  Usando el comando:\n   free -h\n"
  freeSpace1="Su VPS está configurado con"
  freeSpace2="memoria disponible"
  freeSpace3="Parece que actualmente tienes ${YELLOW} `free -h | grep Swap | awk '{print $2}'` ${NONE} espacio de intercambio disponible\n"
  walletConfig="\n${YELLOW}Mostrar daemon Wallet${NONE}\n  Usando el comando:\n          cat $CONF\n"
  logFile="\n${YELLOW}Ver el archivo de registro de Sentinel${NONE}\n  Usando el comando:\n          tail $SENTINEL_LOG\n"
  clearLogFile="\n${YELLOW}Borrar archivo de registro de Sentinel${NONE}\n  Usando el comando:\n          echo \" \" > $SENTINEL_LOG\n"
  dateLogFile="\n${YELLOW}Agregar una fecha al archivo de registro de Sentinel${NONE}\n  Usando el comando:\n          date \" \" >> $SENTINEL_LOG\n"
  startWalletDaemon="\n${YELLOW}Inicia el daemon Wallet${NONE}\n  Usando el comando:\n          $DAEMON -daemon\n"
  firewall="\n${YELLOW}Mostrar reglas de firewall${NONE}\n  Usando el comando:\n   ufw status numbered\n"
  diagCrontab="\n${YELLOW}diagnóstico crontab${NONE}\n Usando el comando:\n   crontab -l\n   Use el siguiente comando para editar\n   crontab -e\n"
  reviewSyslog="\n${YELLOW}Revise el registro del sistema para problemas de Sentinel${NONE}\n  Usando el comando:\n   grep CRON /var/log/syslog | grep $GREPSYSLOG\n"
  reviewCrontab="Revisando crontab para centinela.   $MY_CRONTABREVIEW\n\n${WHITE}Se ve bien${NONE}\n"
  reviewCrontab2="Revisando crontab para que el comando reinicie daemon si se reinicia VPS.\n   $MY_CRONTABREVIEW2\n\n${WHITE}Se ve bien.${NONE}\n"
  reviewCrontab3="${RED}Comprobar crontab - posible error\nno encontró\n$MY_CRONTABREVIEW2\n${NONE}"
  reviewError="${RED}Comprobar crontab - posible error\nno encontró\n$MY_CRONTABREVIEW\n${NONE}"
  crontabContents="\n${YELLOW}contenido crontab${NONE}\n  Usando el comando:\n   crontab -l\n   Use crontab -e to edit\n"
  checkingVPS="${YELLOW}Comprobando la dirección IP externa de VPS / Masternode${NONE}\n  Su dirección IP externa para la configuración de masty / masternode es:: $HOST_IP\n"
  checkingVPS2="  Para verificar que todo esté configurado correctamente, escaneamos el archivo de configuración  [ $CONF ] para la palabra clave $SCANIP\n  Luego comparamos los resultados con HOST_IP [ $HOST_IP ]\n"
  checkingVPS3="\n  Usando el comando:\n   /sbin/ifconfig eth0 | grep Mask | awk '{print $2}'| cut -f2 -d:\n"
  checkingVPS4="\n  Compara esa información con el archivo de configuración [ $CONF ] a su dirección IP Usando el comando:\n   cat $CONF | grep external\n"
  checkingVPS5="Ahora comprobaremos el archivo de configuración de VPS \nEsta se encuentra en $CONF"
  checkingVPS6="${RED}** Puede haber un problema con el externalip $IP_ONLY  .... host IP = $HOST_IP $CONF  ${NONE}\n  Usa el menú para revisar tu archivo de configuración"
  checkingVPS7="${WHITE}externalip parece estar configurado correctamente - $EXTERNAL_IP${NONE}"
  daemonRunning="${RED}** El $DAEMON ya se está ejecutando [ ps -ef | grep -c $DAEMON ] ${NONE}"
  stopWallet="\n${YELLOW}Stop Wallet daemon${NONE}\n  Usando el comando:\n          $CLI stop\n"
  killWallet="\n${YELLOW}Stop Wallet daemon${NONE}\n  Usando el comando:\n          ps -aef | grep $DAEMON | grep -v grep | awk '{print $2}' | xargs kill\n"
  killWallet2="Si no hubo errores, el proceso ha sido asesinado"
  helpOptions="${YELLOW}Nonitor $TOOLNAME help options\n${WHITE} Usando el comando:\n  $TOOLNAME {argument}${NONE}\n"
#aboutInfo="zzz"
aboutInfo="\n${WHITE}+--------------------------------------------------------------+${NONE}\n"
aboutInfo+="${WHITE}| ${CYAN}$COIN Herramientas de Masternode ver: $CURVERSION                  ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE}                                                              ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} ${YELLOW}Las herramientas son gratuitas.                              ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE}                                                              ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} ${YELLOW}Considera usar la dirección de donación si quieres           ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} ${YELLOW}nosotros para continuar el desarrollo de estas herramientas  ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE}                                                              ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} SAROS Donation: SeNPFMFBAPvSV2FFR8Tc2m6dncTE2ky7Se ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} SUCR  Donation: SjUp3Fu2eB7wLBAQkFHs946m1ScWjWXbdr ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} GCH   Donation: GT8bbHhtEQSzgMoXvLeHTf7dL9HwuGVeGY ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} LILI  Donation: GT8bbHhtEQSzgMoXvLeHTf7dL9HwuGVeGY ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE}                                                              ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}+--------------------------------------------------------------+${NONE}\n"
aboutInfo+="\nLas herramientas se han usado $TOTALCTR veces.\n"

 MNENABLED="\n${YELLOW}Mostrar estado habilitado MasterNode.${NONE}\n  Usando el comando:\n   $CLI masternode list full | grep $HOST_IP"

currentRelease="Ya en el lanzamiento actual o más reciente $CURVERSION"
currentRelease2="Una actualización de $CURVERSION a $NEWVERSION está disponible, incluye los siguientes cambios ${GREEN}\n$CHANGES${NONE}"
currentRelease3="Actualicemos: reinicie las herramientas después de la actualización"
currentRelease4="Actualización cancelada."

fi
if [ "$LANGUAGE" = "LANGUAGE=RUSSIAN" ]
    then
declare -ga MainMenu=("" "${CYAN}[1]${NONE} Обзор My MasterNode" "${CYAN}[2]${NONE} Показать меню кошелька"
                "${CYAN}[3]${NONE} Показать меню Sentinel" "${CYAN}[4]${NONE} Меню Masternode"
                "${CYAN}[5]${NONE} Показать системное меню" "" ""
                "${CYAN}[8]${NONE} Показать О нас / Информация" "${CYAN}[9]${NONE} Проверьте новую версию / обновите этот скрипт")
menuDescription="+-ver: $CURVERSION ----------------------+\n"
menuDescription+="| ${CYAN}$COIN Монеты MasterNode Утилиты${NONE}  |\n"
menuDescription+="+----------------------------------+\n"


whatWouldYou="Что ты хочешь, чтобы я сделал?"
selectDescription="Выберите опцию с номером"
pressEnter1="Нажмите enter для продолжения"
pressEnter="Нажмите enter или Option, чтобы продолжить"
walletDaemon="\n${YELLOW}Деймон активен, поэтому мы показываем информацию о кошельке${NONE}\n  Используя команду:\n   $CLI getinfo;\n"
walletSync="\n${YELLOW}Деймон активен, поэтому мы показываем информацию о кошельке${NONE}\n  Используя команду:\n   $CLI mnsync status\n"
walletSync2="${WHITE}Кошелек синхронизирован.${NONE}\n"
walletSync3="${RED}Кошелек не завершил синхронизацию.${NONE}\n"
daemonMustBeRunning1=#"\n${YELLOW}Демон должен быть активным, чтобы выполнить эту команду${NONE}\n  Используя команду:\n   ps -ef | grep -c $DAEMON\n   мы проверяем, работает ли демон\n"
daemonMustBeRunning2="${YELLOW}The daemon $DAEMON must be running to use option\nUse Start Wallet Daemon option${NONE}"
operatingSystem="\n${YELLOW}Operating system version${NONE}\n  Используя команду:\n    cat /etc/issue.net\n"
serverUptime="\n${YELLOW}Время работы сервера.${NONE}\n  Используя команду:\n   uptime"
activeDaemon="\n${YELLOW}Проверьте $DAEMON демон.${NONE}\n\n  Демон - это процесс, который $COIN Сеть, в которой активен ваш MasterNode\n  Он должен быть активным, чтобы получать награды\n\n  Используя команду:\n   ps -ef | grep DAEMON\n"
goodDaemon="${WHITE}Правильное количество $DAEMON демоны${NONE}"
badDaemon="${RED}[Ошибка. Недопустимое количество $DAEMON daemons Бег${NONE}"
mnList="\n${YELLOW}Показать список MasterNode.${NONE}\n  Используя команду:\n   $CLI masternode list addr | grep $HOST_IP\nВы должны найти свой IP-адрес, указанный ниже. Если вы этого не сделаете - ваш MasterNode не зарегистрирован\n\nначать ...."
mnStatus="\n${YELLOW}Деймон активен, поэтому мы показываем информацию о кошельке${NONE}\n  Используя команду:\n   $CLI masternode status\n"
freeSpace="\n${YELLOW}свободное пространство / своп${NONE}\n  Используя команду:\n   free -h\n"
freeSpace1="Ваш VPS настроен с помощью"
freeSpace2="mДоступен emory"
freeSpace3="Похоже, что у вас в настоящее время есть${YELLOW} `free -h | grep Swap | awk '{print $2}'` ${NONE} доступно место подкачки\n"
walletConfig="\n${YELLOW}Показывать демон Wallet${NONE}\n  Используя команду:\n          cat $CONF\n"
logFile="\n${YELLOW}Просмотр файла журнала Sentinel${NONE}\n  Используя команду:\n          tail $SENTINEL_LOG\n"
clearLogFile="\n${YELLOW}Очистить файл журнала Sentinel${NONE}\n  Используя команду:\n          echo \" \" > $SENTINEL_LOG\n"
dateLogFile="\n${YELLOW}Добавить дату в файл журнала Sentinel${NONE}\n  Используя команду:\n          date \" \" >> $SENTINEL_LOG\n"
startWalletDaemon="\n${YELLOW}Запустить демона кошелька${NONE}\n  Используя команду:\n          $DAEMON -daemon\n"
firewall="\n${YELLOW}Отображать правила брандмауэра${NONE}\n  Используя команду:\n   ufw status numbered\n"
diagCrontab="\n${YELLOW}crontab диагностика${NONE}\n  Используя команду:\n   crontab -l\n   Используйте следующую команду для редактирования\n   crontab -e\n"
reviewCrontab="Просмотр crontab для дозорного.\n   $MY_CRONTABREVIEW\n\n${WHITE}Выглядит неплохо.${NONE}\n"
reviewSyslog="\n${YELLOW}Просмотрите системный журнал для проблем Sentinel${NONE}\n  Используя команду:\n   grep CRON /var/log/syslog | grep $GREPSYSLOG\n"
reviewCrontab2="Рассмотрение команды crontab для перезапуска демона при перезапуске VPS.\n   $MY_CRONTABREVIEW2\n\n${WHITE}Выглядит неплохо.${NONE}\n"
reviewCrontab3="${RED}Проверить crontab - возможная ошибка\nне нашла\n$MY_CRONTABREVIEW2\n${NONE}"
reviewError="${RED}Check crontab - possible error\ndid not find\n$MY_CRONTABREVIEW\n${NONE}"
crontabContents="\n${YELLOW}crontab contents${NONE}\n  Используя команду:\n   crontab -l\n   Use crontab -e to edit\n"
checkingVPS="${YELLOW}Проверка внешнего IP-адреса VPS / Masternode${NONE}\n  Ваш внешний IP-адрес для конфигурации шпатлевки / мастера: $HOST_IP\n"
checkingVPS2="  Чтобы проверить, что все настроено правильно, мы сканируем файл конфигурации  [ $CONF ] for the keyword $SCANIP\n  Then we compare the results to the HOST_IP address [ $HOST_IP ]\n"
checkingVPS3="\n  Чтобы найти ваш IP-адрес, мы используем команду:\n   /sbin/ifconfig eth0 | grep Mask | awk '{print $2}'| cut -f2 -d:\n"
checkingVPS4="\n  Сравните эту информацию с конфигурационным файлом [ $CONF ] на ваш IP-адрес Используя команду:\n   cat $CONF | grep external\n"
checkingVPS5="Теперь мы проверим конфигурационный файл VPS\nЭто расположено в $CONF"
checkingVPS6="${RED}** Там может быть проблема с externalip $IP_ONLY  .... host IP = $HOST_IP $CONF  ${NONE}\n  Используйте меню для просмотра файла конфигурации"
checkingVPS7="${WHITE}внешний вид настроен правильно - $EXTERNAL_IP${NONE}"
stopWallet="\n${YELLOW}Демон Stop Wallet${NONE}\n  Используя команду:\n          $CLI stop\n"
killWallet="\n${YELLOW}Демон Stop Wallet${NONE}\n  Используя команду:\n          ps -aef | grep $DAEMON | grep -v grep | awk '{print $2}' | xargs kill\n"
killWallet2="If there were no errors - the process has been killed"
daemonRunning="${RED}** $DAEMON уже бежит [ ps -ef | grep -c $DAEMON ] ${NONE}"
helpOptions="${YELLOW}дисплей $TOOLNAME варианты помощи\n${WHITE} Использовать команду:\n  $TOOLNAME {argument}${NONE}\n"
aboutInfo="${WHITE}+----------------------------------------------------+${NONE}\n"
aboutInfo+="${WHITE}| ${CYAN}$COIN Инструменты Masternode ver: $CURVERSION                  ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE}                                                    ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} ${YELLOW}Инструменты бесплатны.                     ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE}                                                    ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} ${YELLOW}Рассмотрите возможность использования пожертвования, если вы хотите    ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} ${YELLOW}нам продолжить разработку этих инструментов      ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE}                                                    ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} SAROS Donation: SeNPFMFBAPvSV2FFR8Tc2m6dncTE2ky7Se ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} SUCR  Donation: SjUp3Fu2eB7wLBAQkFHs946m1ScWjWXbdr ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} GCH   Donation: GT8bbHhtEQSzgMoXvLeHTf7dL9HwuGVeGY ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE} LILI  Donation: GT8bbHhtEQSzgMoXvLeHTf7dL9HwuGVeGY ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}|${NONE}                                                    ${WHITE}|${NONE}\n"
aboutInfo+="${WHITE}+----------------------------------------------------+${NONE}\n"
aboutInfo+="\nинструменты были использованы $TOTALCTR\n"
MNENABLED="\n${YELLOW}Показать статус включенного мастер-узла.${NONE}\n  Используя команду:\n   $CLI masternode list full | grep $HOST_IP"

currentRelease="Уже в текущем или новейшем выпуске $CURVERSION"
currentRelease2="Обновление с $CURVERSION to $NEWVERSION доступен - он включает следующие изменения: ${GREEN}\n$CHANGES${NONE}"
currentRelease3="Давайте обновим - перезагрузите инструменты после обновления"
currentRelease4="Обновление отменено."

fi
declare -ga MainMenuOptions=("" "ReviewMyMasterNode" "ShowWalletMenu" "ShowSentinelMenu"
                            "ShowMasterNodeMenu" "ShowSystemMenu"
                            "" "" "ShowAbout" "Upgrade")
declare -ga MainMenuType=("" "" "M" "M" "M" "M" "" "" "" "")

CMDLINE="$1"
response="1"

if [ "$CMDLINE" = "/?" ]
  then
    CMDLINE="/help"
  elif [ "$CMDLINE" = "--help" ]
    then
      CMDLINE="/help"
fi

## Routine passed as parameter
if [ "$CMDLINE" != "" ]
  then
  if [ "$CMDLINE" != "/help" ]
  then
    #lcCMDLINE="${CMDLINE,,}" # Change all functions to lowercase
    lcCMDLINE="$CMDLINE"
    if [ "$(type -t $lcCMDLINE)" = 'function' ]; then
      ##$CMDLINE
      $lcCMDLINE
      exit
    else
      echo -e "${RED}Command / function not available${NONE} $lcCMDLINE"
      exit
    fi
  fi
fi

##if [ "$CMDLINE" == "/help" ]
##  then
##  response="" ## exit loop after /help
##else
  response="1" ## loop for more options
##fi

until [ "$response" == "" ]; do
echo -e "$menuDescription"

    if [ "$CMDLINE" == "/help" ]
      then
        echo -e "$helpOptions"
        #"${YELLOW}Display $TOOLNAME help options\n${WHITE} Use command\n  $TOOLNAME {argument}${NONE}\n"
    fi

  zi=0
  for i in "${MainMenu[@]}"
  do
    if [ "$CMDLINE" == "/help" ]
      then
        chrlen=${#MainMenuOptions[$zi]}
        if [ "$chrlen" -gt "14" ]
          then
          tab="\t"
        else
          tab="\t\t"
        fi

        echo -e "${MainMenuOptions[$zi]} $tab$i"
        response=""
        ## Display nested menu
        if [ "${MainMenuType[$zi]}" == "M" ]
          then
            ${MainMenuOptions[$zi]}
        fi
    else
          echo -e "$i"
    fi

    let "zi = $zi + 1"

  done

    if [ "$CMDLINE" != "/help" ]
      then
        echo -e "${BOLD}$whatWouldYou${NONE}"
        read -p "$selectDescription " response
        echo
        ${MainMenuOptions[response]}
    fi

done

