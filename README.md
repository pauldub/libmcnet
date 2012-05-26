libmcnet
========

An event based, zero-copy, portable Minecraft network protocol parser.

Overview
--------

libmcnet makes the life of the average Minecraft server/client developer easier
by providing a convenient, stable interface for parsing Minecraft network data.
It stays out of your way as much as possible, and assumes pretty much nothing
about the implementation of the code using it. To this end, it is very much not
a "batteries included" library. You have to take care of things like encryption,
character set conversion, and even some metadata parsing on your own.

Don't worry. It's not too hard.

Features
--------

1. Simple interface
2. Interruptible parser
3. No memory allocations
4. Coherent, hackable implementation

Documentation
-------------

To do! Oh no! For now, please look at the example.

Example
-------

Also see [example.c](example.c).

```c
#include <stdio.h>
#include <stdlib.h>

#include "src/parser.h"
#include "src/packets.h"

void on_packet(mcnet_parser_t* parser, mcnet_packet_t* packet) {
  printf("[%p] packet type: %02x\n", parser->data, packet->pid);

  if (packet->pid == 0x00) {
    mcnet_packet_00_t* packet_00 = (mcnet_packet_00_t*)packet;
    printf("-> id: %d\n", packet_00->id);
  }
}

void on_error(mcnet_parser_t* parser, int err) {
  printf("[%p] error %d\n", parser->data, err);
}

int main() {
  mcnet_parser_t parser = { .data = NULL };
  mcnet_parser_settings_t settings = { .on_packet = on_packet, .on_error = on_error };

  char data[] = { 0x00, 0x00, 0x00, 0x00, 0x01, 0x00, 0x00, 0x00, 0x00, 0x02 };

  size_t nparsed = 0, offset = 0;

  while ((nparsed = mcnet_parser_execute(&parser, &settings, data + offset, 10 - offset)) != 0) {
    printf("parsed %d bytes\n", nparsed);
    offset += nparsed;
  }

  return 0;
}
```

License
-------

3-clause BSD. A copy is included with the source.

Contact
-------

* GitHub (http://github.com/deoxxa)
* Twitter (http://twitter.com/deoxxa)
* Email (deoxxa@fknsrs.biz)