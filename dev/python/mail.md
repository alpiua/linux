mail
====

from email.Parser import Parser
from sys import stdin, stdout

message = Parser().parse(stdin)
if not message.is_multipart():
    stdout.write(message.get_payload(decode=True))
else:
    for part in message.get_payload():
        if part.get_content_type() == 'text/plain':
            stdout.write(part.get_payload(decode=True))