#### Requirements:
• IDs must be unique.  
• IDs are numerical values only.  
• IDs fit into 64-bit.  
• IDs are ordered by date.  
• Ability to generate over 10,000 unique IDs per second.  


#### Multi-master
![multi_master](https://puml-demo.herokuapp.com/github/AndreiYu/systems_design/blob/master/id_generator/multi_master.puml)  

#### UUID
Pros:  
* Generating UUID is simple. No coordination between servers is needed so there will not be any synchronization issues.  
* The system is easy to scale because each web server is responsible for generating IDs they consume. ID generator can easily scale with web servers.  
Cons:  
* IDs are 128 bits long, but our requirement is 64 bits.  
* IDs do not go up with time.  
* IDs could be non-numeric.  

#### Ticket service
![ticket_service](https://puml-demo.herokuapp.com/github/AndreiYu/systems_design/blob/master/id_generator/ticket.puml)

#### Twitter Snowflake  
Instead of generating an ID directly, we divide an ID into different sections.  
![snowflake](https://puml-demo.herokuapp.com/github/AndreiYu/systems_design/blob/master/id_generator/snowflake.puml)  

Datacenter IDs and machine IDs are chosen at the startup time. Any changes in datacenter IDs and machine IDs require careful review since an accidental change in those values can lead to ID conflicts.   
Timestamp and sequence numbers are generated when the ID generator is running.  
The maximum timestamp that can be represented in 41 bits is   
```2 ^ 41 - 1 = 2199023255551 milliseconds (ms), which gives us: ~ 69 years```   
Sequence number is 12 bits, which give us ```2 ^ 12 = 4096 combinations```. This field is 0 unless more than one ID is generated in a millisecond on the same server. In theory, a machine can support a maximum of 4096 new IDs per millisecond.

### Reference materials
1) [Snowflake](https://blog.twitter.com/engineering/en_us/a/2010/announcingsnowflake.html)