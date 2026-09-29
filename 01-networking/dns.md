# DNS

[← Back to README](../README.md)

## What is it?
**DNS (Domain Name System)** translates human-readable domain names into IP addresses, so the browser knows where to find the website.

```
www.example.com  →  DNS lookup  →  93.184.215.14
```

**Analogy:** DNS works like a phone book. You look up a name and get the number.

## Key facts
- A domain name **points to** an IP address. They are not the same thing – the name is for people, the IP address is for computers.
- DNS uses **port 53**.

## In Azure
- Azure reserves two addresses in every subnet for Azure DNS (see [subnetting](ip-addressing-subnetting.md#subnetting-in-azure-29-example))
- **Azure DNS** hosts public domains
- **Azure Private DNS zones** resolve names inside vNets

## Related
- [Network Fundamentals](network-fundamentals.md)
