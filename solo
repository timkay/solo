#!/usr/bin/perl -s
#
# solo v1.5
# Prevents multiple cron instances from running simultaneously.
#
# Copyright 2007-2010 Timothy Kay
# http://timkay.com/solo/
#
#    This program is free software: you can redistribute it and/or modify
#    it under the terms of the GNU General Public License as published by
#    the Free Software Foundation, either version 3 of the License, or
#    (at your option) any later version.
#
#    This program is distributed in the hope that it will be useful,
#    but WITHOUT ANY WARRANTY; without even the implied warranty of
#    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
#    GNU General Public License for more details.
#
#    You should have received a copy of the GNU General Public License
#    along with this program.  If not, see <http://www.gnu.org/licenses/>.
#

use Socket;

alarm $timeout								if $timeout;

$port =~ /^\d+$/ or $noport						or die "Usage: $0 -port=PORT COMMAND\n";

if ($port)
{
    $addr = pack(CnC, 127, $<, 1);
    print "solo: bind ", join(".", unpack(C4, $addr)), ":$port\n"	if $verbose;

    $^F = 10;			# unset close-on-exec

    socket(SOLO, PF_INET, SOCK_STREAM, getprotobyname('tcp'))		or die "socket: $!";
    bind(SOLO, sockaddr_in($port, $addr))				or $silent? exit: die "solo($port): $!\n";
}

sleep $sleep if $sleep;

exec @ARGV;
