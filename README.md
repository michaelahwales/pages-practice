# Apollo Auto Architecture Research: Group 13

## Group Members:
- Inika Chikarmane
- Rebecca De Venezia
- Rebecca Henry
- Ensor Moriarty
- Michaelah Wales
- Hannah Weider

# Assignment 1
## Conceptual Architecture
### ABSTRACT
Uncovering the conceptual architecture of a system is of interest in order to learn how
developers tackle design problems. When deconstructing an existing system, one can gain insight
into how development, data flow, testing, and reusability can maximize efficiency and minimize
“spaghetti code” that is difficult to follow. The aim is not to look at source code or
implementation details but to extract an architecture that follows best practices and suits the
domain. This report concerns itself with the conceptual architecture of Apollo Auto, a system in
continuous development created with the aim of becoming a safe platform for autonomous
driving.

We strove to answer salient questions about software design. For example, what
architecture styles best suit the overall architecture for the domain of self-driving vehicles? How
would a system for self-driving cars be designed so that it could work for most vehicles, without
redesign? What is the best way to design data flow so that the vehicle could react quickly in
emergency situations? How would an autonomous vehicle system use concurrency to maximize
resource usage? We studied available documentation on the conceptual architecture of Apollo
Auto as well as descriptions of its features along with research on the ideal architecture for
autonomous vehicles to answer these questions and more.

In short, we discovered that Apollo Auto was intelligently designed, with layers to
describe the logical division of components, hardware and software decoupled for maximum
reuse, an event-handling system for data flow, and a framework for parallel computing. The
documentation process was an excellent exercise in understanding software efficiency and
designing for a specific domain while staying true to design practices (such as minimized
cross-interactions) that benefit all systems.

### INTRODUCTION AND OVERVIEW
Apollo Auto has made incredible advancements in autonomous driving technology. So
far, the system has been applied to cars, buses, and taxis. The autonomous cars can park
themselves, reduce the time spent waiting in traffic by up to 30% by using traffic flow to
minimize the amount of red lights, share location information with other cars, use sensors of all
kinds to accurately sense its environment, and more (“Smart Transportation Solution”). Apollo
Auto boasted that by the end of 2020, the software would be able to handle highways and typical
urban streets (“Apollo's Mission”).

The purpose of this report was to extract the conceptual architecture of Apollo Auto,
referring to reference architectures on autonomous driving systems and methods of extracting
architectures from documentation. The report uses an “inverted triangle” approach, beginning
with a description of the high-level architecture styles that characterize the system, then focusing
in on architecture styles that define subsystems and data flow. First, the derivation process of the
overall conceptual process is described, along with styles that were considered but ultimately
swapped out. Next, the final design for the conceptual process is detailed, along with diagrams,
descriptions of crucial components, their general interactions, and external interfaces used. What
follows is a deep dive into the evolution of the system, the data flow between parts, and how
concurrency operates within the system. Finally, the flow of usage is demonstrated with
sequence diagrams for salient use cases and the division of responsibilities among programmers.

Throughout the documentation process, we discovered that autonomous driving systems
must carefully consider both the software and hardware components in its design. Optimally, the
software and hardware components can be cleanly split into separately functioning layers.
Additionally, in order for the vehicle to stay up-to-date on traffic information and navigate its
environment safely, a Client-Server architecture style that describes a cloud that multiple
vehicles could connect to is ideal. The Publish-Subscribe style also enables the vehicles to react
to events quickly without wasting processing power, which is relevant to data flow. Finally, the
Process-Control style describes the methodology for ensuring that certain vehicle parameters
stay within a safe balance.

We also discovered that it is important to carefully consider the documentation sources
from which to extract a conceptual architecture. Even documentation from certified developers
that appears to be conceptual may be too closely coupled with concrete implementation, and may
not reflect the designer’s initial ideas. We found it useful to refer to higher-level descriptions of
the Apollo Auto system, as well as reference architecture that abstracts the most crucial parts of
the domain-specific design.

In short, we believe to have developed an accurate design that reflects the wishes of
Apollo Auto stakeholders and stays true to good practices for intelligent autonomous vehicle
systems.

### CONCEPTUAL ARCHITECTURE
<h4 align="center">
   DERIVATION AND HIGH-LEVEL OVERVIEW
</h4>
<h5 align="center">
  <em>Derivation Process</em>
</h5>
To derive the conceptual architecture, we first compiled a multitude of online resources
that described Apollo Auto’s architecture and how the system functioned. These included
conceptual documentation available from the company’s Github, reference architecture for
self-driving cars, Apollo Auto’s website, video demos, and various articles that described the
technical advancements that the company has made.

The reference architecture for autonomous vehicles by Behere and Törngren was an
excellent resource when determining the most modular way to describe an autonomous driving
architecture. When deriving our final conceptual architecture, we referred to this architecture as
it claimed to be successful for general autonomous vehicle implementations. Our goal was to
“instantiate” Behere and Törngren’s proposed structure with conceptual information available
about Apollo Auto in order to derive a conceptual architecture that was proven to be secure and
efficient to test and develop.

<h5 align="center">
  <em>Other Styles Considered</em>
</h5>

First, let us discuss styles that we considered but that were ultimately substituted for
better options. These styles include Pipe and Filter and Interpreter. First, Pipe and Filter was
considered because of how data appears to flow through the system. When examining Apollo
Auto’s documentation, it was clear that information is constantly flowing from the cloud to the
software to the hardware to the user interface and vice versa, along with calculations being done
along the way. It was initially thought that data could be traveling through pipes with incremental
filtering along the way, with the rationale that perhaps urgent data could flow to the vehicle
quicker if calculation was done incrementally, saving time in crucial emergency situations.
However, this potential advantage is overshadowed by the fact autonomous driving systems are
highly interactive and each component may need to notify another at a moment’s notice. This is
not descriptive of a Pipe and Filter style, which does not use an “event” system and whose
components are not deeply aware of what the other components do.

Next, Interpreter Style was considered, the rationale being that the language for
manipulating vehicle components might not be directly available, as commands may have to be
interpreted differently for different types of vehicles. However, a journal paper on autonomous
driving systems states: “The vehicle contains a network of electronic control units (ECUs)
controlling the basic vehicle propulsion (lateral and longitudinal acceleration, braking). The
vehicle manufacturer usually builds a ‘gateway’ that allows the experimenters to send a limited
set of commands to the ECUs in the vehicle network” (Behere and Törngren 6). Thus, most
vehicle manufacturers provide functionality for developer input for direct access to vehicle
hardware, eliminating the need for the software to have a deep understanding of the
vehicle-specific implementation (more on this later).

<h5 align="center">
  <em>The Final Conceptual Architecture</em>
</h5>
<img src="/docs/assets/conceptual_diagram_v4.png" />

<p align="center">
   <strong> Figure 1 </strong>: Our final conceptual architecture
</p>

In order of decreasing abstraction, the architectural styles used for the final conceptual
architecture were Client-Server, Layered, Publish-Subscribe, and Process-Control 
(see the Data Dictionary for full definitions of these architectures). An overview
of the system’s components and their interactions will now be described, 
referencing these systems as necessary.

To begin, the Cloud Service system, which consists of components crucial to autonomous
driving technology, uses a Client-Server system to interact with the vehicle and keep it safe.
Firstly, the HD-Map uses deep learning technology to create incredibly accurate maps of the
vehicle’s surroundings that the vehicle’s software can query on the fly for navigation (“Apollo
Open Platform”). Vehicles would also use the cloud in order to interact with each other and
decrease the chance of collisions. Additionally, the V2X (Intelligent Vehicle Infrastructure
Cooperation Solution) component utilizes the cloud to retrieve local traffic data in real time
(“V2X: Intelligent Vehicle Infrastructure Cooperation Solution”).

Next, Apollo Auto uses the Layered architecture style in order to sort its components into
logical tasks. This was derived by taking note that the components of an autonomous driving
system should be partitioned into a layer for cognitive driving intelligence (the logic that
determines how the car should operate) and a layer for the vehicle platform (the hardware of the
vehicle itself). Upon looking at Apollo Auto’s documentation, the system follows this rule by
partitioning the software and hardware subsystems:

<p align="center">
   <strong> Figure 2 </strong>: Decoupling of the cognitive driving intelligence, the vehicle 
   hardware platform, and the cloud shown in Apollo Auto’s architecture (“Apollo Open Platform”)
</p>

As briefly previously mentioned, the decoupling of the cognitive driving intelligence and
the vehicle platform is crucial to efficient development. The intelligence system does not have to
know about platform-specific details and vice versa, allowing the software to easily be deployed
to multiple vehicle types without too much re-programming. We must note, however, that such a
decoupling assumes that the vehicle hardware has “abilities for keeping the platform stable and
retaining basic self-protection measures which may include reactive control” (Behere and
Törngren 7). However, the majority of high-end vehicles come with these abilities built-in,
allowing for cleanly split layers in the general case. Additionally, the cloud system used by
Apollo Auto can be seen as another layer that the software interacts with.

Before we look further within, let us describe the main modules present in Apollo Auto in
order to get acquainted with the system. The Localization component makes use of the GPS,
LiDAR (Light Detection and Ranging), Camera and IMU (Inertial Measurement Unit)
components from the vehicle hardware to generate the vehicle’s exact location. The Perception
component functions as the vehicle’s “eyes,” discerning obstacles and the status of traffic lights.
Then, the Prediction component predicts the next move of each detected obstacle, and the
Planning component calculates the future trajectory of the parent vehicle. The Control
component translates the output from the Planning component into actionable commands for the
vehicle's throttle, brakes, and wheel; these are then propagated to the vehicle hardware through
the CanBus. The vehicle hardware contains the CPU, GPS, IMU, Camera, LiDAR, and multiple
Radars that collect sensor input as data to make decisions from (“Apollo 5.5 Software
Architecture”).

Publish-Subscribe is the intuitive choice for communication between these components.
First, the system would ideally be applicable to multiple types of vehicles instead of being
designed in a vehicle-dependent fashion. Designing a system that would have to be re-designed
for each vehicle type would waste time and expenses. However, if we think of the autonomous
driving system as a vehicle with a separate “conscious” system, we can avoid the issue by
publishing events from the conscious system (i.e., planning and prediction) that the vehicle
hardware can subscribe to and respond accordingly by altering its velocity. The autonomous
driving system could be seen as a “plug-in” to the vehicle hardware, which Publish-Subscribe is
designed for.

Additionally, Publish-Subscribe is ideal for a system that is designed to react quickly to
its environment without using expensive busy-waiting that would take up processing power. The
architecture style allows components in the system to be notified of events that could happen at
any moment. For example, the Perception component could detect an unexpected swerving
vehicle, Prediction catches the event and detects their next move, Planning in turn decides the
vehicle’s trajectory, which notifies Control then the CanBus, which handles the action of
avoiding the other vehicle.

Finally, Apollo Auto uses a Process-Control architecture style as the CanBus passes data
from the vehicle hardware back to the software system (“Apollo 5.5 Software Architecture”).
This Process-Control subsystem is likely used to “continuously learn and adjust the model
parameters” (Behere and Törngren 9). This could be used to keep parameters such as vehicle
speed and acceleration within a determined balance.

<h5 align="center">
  <em>External Interfaces</em>
</h5>

Other modules for testing and error handling purposes interact with the whole system.
Serviced by the vehicle’s Hardware Platform, the Open Vehicle Certificate Platform refers to the
interface that connects the Apollo Auto autonomous driving system with the car itself, which is
at the highest level of the system. (“Open Vehicle Certificate Platform”). The Human Machine
Interface (HMI), or DreamView, is an Open Software component that allows drivers and
developers to track and view the status of the autonomous vehicle in real time, updated by the
Monitor. The Monitor component keeps tabs on the entire system and watches for errors, alerting
the Guardian component if anything goes wrong, which would then intercede and take over
(“Apollo 5.5 Software Architecture”). Finally, the Cloud Service layer described above is
another example of an external interface.

<h4 align="center">
   EVOLUTION OF THE SYSTEM
</h4>

The Apollo system continuously improves its prediction models by generating semantic
maps that add data on road features to its deep learning network (“Apollo 5.0 Technical Deep
Dive”). These maps help improve the accuracy of models like behavior predictions, path
prediction, intersecting turning direction prediction, and more. A hierarchy of the models, ranked
by the priority of obstacles, is maintained and passed through scene analysis, intent
understanding, task prioritization and through the model scheduling framework (“Apollo 5.0
Technical Deep Dive”). The system then automatically selects a model that will result in the best
prediction currently possible. This process allows the system’s models to evolve so prediction
results can continuously improve.

As new road conditions and driving use cases are introduced to the system, the planning
component evolves to become more modular and scenario specific. This means that each driving
use case can be addressed individually so that scenario-specific issues can be reported and
resolved independently from other scenarios (“Apollo 5.5 Software Architecture”). The planner
also uses optimization algorithms to improve the distribution of speed and acceleration over
time, and the Open Space Planner uses a model-based optimization scheme that represents
obstacle shapes and road boundaries to utilize the vehicle dynamics model more efficiently
(“Apollo Open Platform”). Similarly, AI techniques and ‘data-driven solutions’ allow the
perception component to improve its detection and recognition capabilities over time (“Apollo
Open Platform”). Overall, by continuously optimizing and updating its components, the system
is able to evolve by improving its prediction models, perception, planning, and overall
performance over time.

<h4 align="center">
   CONTROL AND DATA FLOW AMONG PARTS
</h4>
<h5 align="center">
  <em>Implicit Invocation and Process-Control Architecture for Data Flow</em>
</h5>



## Related Links:

### Overview:
[Apollo Github Repository](https://github.com/ApolloAuto/apollo)  
[Apollo Release Notes](https://github.com/ApolloAuto/apollo/releases)

### Mission Statement:
[Apollo's Mission](https://www.youtube.com/watch?v=UmKSiFujJiw)

### Demos:
[Complete Urban Journey in a Baidu Apollo Autonomous Vehicle](https://www.youtube.com/watch?v=EY3yVgLecf0)  
[Testing Apollo Auto - DreamView Demo](https://www.youtube.com/watch?v=JWoNMFsPRH8)

### Group Policies:
[Apollo Manifesto (Data Sharing Policies)](https://apollo.auto/docs/promise.html)  
[Apollo Governance Policies](https://apollo.auto/docs/manifesto.html)

### Software Architecture:
[Software Specifications Github Directory](https://github.com/ApolloAuto/apollo/tree/master/docs/specs)  
[Overview of software components](https://apollo.auto/developer.html)  
[Apollo's Cyber Security](https://apollo.auto/platform/security.html)  
[Apollo's Compatibility](https://apollo.auto/vehicle/certificate_en.html)  
[Inside Apollo 6.0: A Road Towards Fully Driverless Technology (Report on Company Architecture)](https://medium.com/apollo-auto/inside-apollo-6-0-a-road-towards-fully-driverless-technology-522b2b4295cc)  
[RTI brings communications software to Baidu Apollo autonomous driving ecosystem (Information Sharing)](https://www.therobotreport.com/rti-communications-software-joins-baidu-apollo-self-driving/)

### Articles and News:
[Concordia researchers dig deep into the software powering self-driving cars](https://www.concordia.ca/news/stories/2020/11/03/concordia-researchers-dig-deep-into-the-software-powering-self-driving-cars.html)  
[Building a self-driving car that people can trust](https://www.technologyreview.com/2020/12/16/1014672/building-a-self-driving-car-that-people-can-trust/)  
[Apollo from a hardware perspective](https://www.neousys-tech.com/en/discover/fanless-in-vehicle-pc/baidu-apollo-open-source-autonomous-driving-platform)  
[Baidu May Reach Fully Autonomous Driving Faster than Tesla](https://www.youtube.com/watch?v=Zcw70M51FbM)  
[Initiating the war on car information security, Baidu Apollo builds the highest level of security protection shield](https://inf.news/en/auto/0fd3a2aac718d068c3fe570e54829ebc.html)


### Tutorials:
[Official development/engineering tutorial](https://apollo.auto/devcenter/devcenter.html)  
[Self-Driving Car Fundamentals Introductory Video](https://www.youtube.com/watch?v=Tsr2iSA8TgA)  
[Self-Driving Car Fundamentals Course](https://www.udacity.com/course/self-driving-car-fundamentals-featuring-apollo--ud0419?utm_source=youtube&utm_medium=social&utm_campaign=apollo)  
[How To Build A Self Driving Car (Miniature)](https://www.youtube.com/watch?v=cB_ez2MNHMo)

### Simulations:
[SVL Simulator by LG](https://www.youtube.com/watch?v=Ucr0aM334_k&feature=youtu.be)  
[Apollo Game Engine Based Simulator](https://apollo.auto/gamesim.html)

### Textbooks:
[Performance, Security, and Safety Requirements Testing for Smart Systems Through Systematic Software Analysis](https://deepblue.lib.umich.edu/bitstream/handle/2027.42/151693/kehong_1.pdf?sequence=1)


## Assignments:

- [x] &nbsp; A0: Create a group of 6 (on OnQ) and group website 
- [x] &nbsp; A1: Document the conceptual architechture
- [ ] &nbsp; A2: Recover the concrete architechture and compare to the conceptual
- [ ] &nbsp; A3: Propose an enhancement, then propose and compare 2 designs / implementation plans


## Project Mark Breakdown:
1. Submit group of 6 on onQ (otherwise random)                           
> 21 Jan 
3. Create & share group website (+ group name)                  
> 4%  31 Jan 
5. Conceptual Arch. Report [15p.] & Pres. [10'] (A1)         
> 14+8%  18 Feb 
7. Concrete Arch. Report [15p.] & Pres. [10'] (A2)           
> 14+8%  18 Mar 
9. Arch. Enhancement (A3) - talk with your TA                    
> week of 21 Mar 
11. Arch. Enhancement Report [15p.] & Pres. [10'] (A3)         
> 14+8%  8 Apr

Source: Week 1 Lecture Slides and Syllabus, page 30 and 37
