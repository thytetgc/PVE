#!/usr/bin/perl
print "\033[2J";    #clear the screen
print "\033[0;0H";  #jump to 0,0

use strict;
use warnings;

my $mode = "mail";
if (@ARGV && ($ARGV[0] eq "print")) {
    $mode = "print";
}

my $node = `hostname -a`;
chomp $node;
my @vmids = `pvesh get /nodes/$node/qemu/ --output-format json-pretty | jq -r '.[] | .vmid'`;

my $fstrim_exec = "";
my $fstrim_exec_filtered = "";
my $message = "";

foreach my $i (@vmids) {
    chomp $i;
    my $status = `qm status $i`;
    chomp $status;
    next if $status ne "status: running"; 
    if ($mode eq "print") {
	$fstrim_exec = `qm guest exec $i fstrim -- "-av"`;
	print "Resultados Fstrim para $i VM \n $fstrim_exec \n";
    } else {
	$fstrim_exec = `qm guest exec $i fstrim -- "-av"`;	
	$fstrim_exec_filtered = `qm guest exec $i fstrim -- "-av" | awk -F":" '/exited/{print \$2}'`;
	if ($fstrim_exec_filtered eq "") {
	#Este código de erro significa que o agente convidado QEMU não está em execução
	#Teste isso executando na VM systemctl stop qemu-guest-agent.service
	    $message .= "O agente convidado QEMU não está em execução para VM $i no nó $node\n";
	    $message .= "Mensagem de erro bruta: \n $fstrim_exec \n";
	} elsif (index($fstrim_exec_filtered, "0") != -1) {
	    $message .= "Mesmo o agente convidado QEMU está em execução, há um erro de execução do comando fstrim para VM $i no nó $node\n";
	    $message .= "Mensagem de erro bruta: \n $fstrim_exec \n";
        }
    }
}

if ($mode eq "mail" && $message ne "") {
    my $to = "root";
    my $fullhostname = `hostname -f`;
    $fullhostname =~ s/^\s+|\s+$//g ;
    my $from = "root@".$fullhostname;
    my $subject = "Relatório de descarte Fstrim no nó Proxmox $node";

    open(MAIL, "|/usr/sbin/sendmail -t");

    # Email Header
    print MAIL "To: $to\n";
    print MAIL "From: $from\n";
    print MAIL "Subject: $subject\n\n";
    # Email Body
    print MAIL $message;

    my $result = close(MAIL);
    if(!$result){
	#ToDo write to logs
	#print "Houve um problema!\n";
    }
}
