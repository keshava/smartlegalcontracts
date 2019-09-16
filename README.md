# COOP CHARTA

**1st Lexon Legal Hackathon**  
14 - 19 Sept 2019 / Cyberspace

**Aeternity Universe One Haeckathon**  
Event hosted in Prague, 14 + 15 Sept 2019

## Team

**Anja Bajl**  
**Arjan van Eersel**  
**Marina Markezic**  
**Henning Diedrich**  

## Objective

<font color=darkred>**Create a _legal_ _and_ _smart_ charta for a cooperative.**</font>

Cooperatives are democratic, legal entities that can have a budget and own things, that allow people to buy shares in them when they become members and sell them when they quit their membership, realizing possible profits from the value increase of the cooperative.

Lexon allows for a cooperative charta to be both a legal document and a smart contract, executable on the blockchain. This gives legal certainty in regards to joint ownership of property of the cooperative members. The DAO that is formed by the charta is a legal person that can de-facto and by rights own assets.

The general purpose of Lexon is to make the position of smart contracts stronger in the real world, off-chain. If the code of a contract is readable and clearly reflects an agreement, the incentive for anyone to sue anyone else over the actions of a smart contract is reduced: because if the human understanding of the text is the same as what was executed on the blockchain, a judge will be much less inclined to declare the IT part of it void.

This project spans the entire arc from ideating to application implementation, however, it adds a new first step, that of discussion of legal aspects and compliance. In so far as we managed to explore the legal aspects thoroughly, and spanned this hole wider arc, the project can already be counted as successful blueprint for future legal engineering hackathons.


## Result

**All bases were worked, especially:**

* a [generic cooperative charta](assets/charta.odt) for the cooperative
* a first reformulation of the [charta in Lexon](assets/charta.lex.pdf)
* [Sophia smart contract](assets/charta.aes) code as spec for Lexon
* [Vue-based DApp](src/views/Home.vue) for this basic functionality

See immediatly below for instructions for the live demo.

Please note that his doc and its s a central part of the results, on par with the DApp live demo. 

In the interest of time the DApp shows a modest first step, not the entire charta.

Having beaten the complete path, even just for a functionally very reduced example was one central premise of this project.

Eventually an DApp as shown below is to be created following the process here described to arrive at a legally sound implementation of a cooperative DAO that can own assets and interact as a legal person.

On a higher level, even the complete project will be but one example for this process to join real and blockchain world.

### Live Demo

#### Phone


#### Desktop



## Sequence of Translations

**The sequence is legal > Lexon > Sophia > DApp.** 

The following screenshots illustrate the four steps and how the code evolves from legal prose, through the Lexon syntax to a Sophia smart contract and finally, finds its expression on the mobile surface of a decentralized application.

1. ### Legal

This is the marked up legal text. Find the full document [here](assets/charta.odt).

![legal](images/legal.png)

2. ### Lexon

This is the same logic as the document before but articulated in Lexon. Find the full document [here](assets/charta.lex.pdf).

![Lexon](images/lexon.png)

3. ### Sophia

The following is the same charta gain, now articulated in Aeternity's blockchain programming language. Find the [full example](assets/charta.aes) here.


![Sophia](images/sophia.png)

4. ### Dapp

Finally, these are screenshots from the DApp that uses the smart contract backend. You find the [source code here](src/views/Home.vue).

See above for the link and explanation of the live demo.

![Dapp](images/dapp.png)



## Building

### Requirements 

* npm
* vue 
* axios
* aeternity SDK

as of 9/15/19

### Building

Install the dependencies

	npm install

Once the setup is completed you can simply run

	npm run dev

### Known issues:

* The whole contract state is being read into the aepp. This is implemented as a workaround for: https://github.com/aeternity/hackathon-prague/issues/8


## Work

Work had five phases

1. **Legal** charta:  
	As intended, the legal deliberations were held first, took up the biggest chunk of time and included more of the team than the later phases.
	1. a coop charta was analyzed and marked up, in two parallel work-streams to come to an understanding how much of the legal prose could be reasonably become automated on the blockchain, i.e. compiled to Sophia by Lexon.  
  	2. a [minimal, generic cooperative charta](assets/charta.odt) was created to serve as a starting point for the next step
  	3. research into jurisdictions - Slovenia and UK - was started to understand specific requirements especially regarding weighed votes. 

2. **Lexon** digital charta:
	1. a subset of functionality was chosen to implement during the available time horizon: foundation, member application, handling, and leaving the cooperative.
	2. [Lexon code](assets/charta.lex.pdf) was written that expresses this minimal functionality. This was dubbed 0.2.21, i.e. one step beyond the current implementation 0.2.20.

3. **Sophia** smart contract:
	1. [Sophia code](assets/charta.aes) was written that reflects the Lexon code exactly, based on the understanding of how it is generated by the Lexon compiler.
	2. The Sophia code was slightly modified to work around known issues in the full stack elements.

4. **Vue** front end:  
a [Vue front end](src/views/Home.vue) was built that incorporates the smart contracts and allows users to interact with it.

5. **Documentation** and upload


### Roadmap

The intent is to finish this project, which will take considerable resources but seems very well worth it to create a very useful template for an application of Lexon.

6. **Lexon** Compiler:  
Use Step 3 to bring the Lexon compiler to the next level.

7. **Complete Charta**:  
Iterate through the remaining articles of the legal charta to bring it on-chain. This has turned out to be a much more involved effort than anticpated.

8. **Production**
We want to work with and support who would like to use this project in production for an actual cooperative.

## Findings

* #### Deep Automation
	The share of text of a cooperative charter that can be automated to the blockchain is **unexpectedly high, at about 50%.** At the same time the effort will be much bigger than anticipated, concretely, not feasable within two days.

* #### Democratic Complexity
	The areas and modes of voting that a cooperative charter touches upon in 30 pages of text are many and they need to be articulated in more detail when expressing them in Lexon.

* #### Structural Complexity
	Even a relatively simple process like an application can ask for an act of data modelling to be expressed in Lexon. Therefore, Lexon 0.2.20 did not suffice.

* #### Clean Slate
	Because of its required document structure it did not work to insert Lexon code into the existing charter bit by bit. Instead, we resorted to a clean slate approach. The hope is that with more experience in programming Lexon, a more incremental approach might come in reach.

* #### Visual Departure
	It would seem now that a Lexon charta for a cooperative will look and be structured very different than a 'normal' charta in legal prose. This is probably owed to the higher procedural detail that Lexon requires to have spelled out and that can remain vague and much briefer in a generic (prose) template of a charta.

* #### Waffle Effect
	Many places in the charta started to look like oscillating between meaning for the blockchain and the real world. They could describe both on-chain mechanisms and at the same time be valid description of other events off-chain. <font color=teal>**This indicates that the scope of Lexon extends beyond its intended purpose.**</font>
    
    This "Waffle Effect" is a decidedly new phenomenon but analogous to both the fundamental abstraction of laws and object orientation in programming.

* #### Lexon passion
	Lexon is sufficiently exciting to align a group of strangers over one weekend, and navigate uncharted terrains for a very ambitious task.
    
* #### Aeternity Sophia
	The functional language Sophia is a great language to program safe smart contracts in. Different versions of documentation online and minor known issues can make life difficult, nothing that will last. The safety has the usual cost of some verbosity to achieve updates that are trivial in lesser languages.

* #### Aeternity maturity
	Aeternity and its ecosystem turns out to be mature enough to deploy production-level projects. The engineering that can be observed when looking under the hood is impressive, including the implementation of its virtual machine. Encountered problems tend to misunderstandings. The very fact that outdated links and docs get in the way points to the steady progress that is happening.



## Resources

* Coop Chart Dapp Live Demo  
  * see above for link and explanation  


* Lexon Online Editor  
  * http://3.lexon.tech  


* Sophia Online Editors  
  * we used https://testnet.contracts.aepps.com  
  * alternative: http://fireeditor.nikitafuchs.de  
    

* Sophia off-line compiler  
  * minimal overhead: https://github.com/aeternity/aesophia_cli  



## License

    LEXON COOP CHARTA PROJECT

    Copyright (C) 2019  Anja Bajl, Arjan van Eersel, Marina Markezic, 
                        Henning Diedrich

    This program is free software: you can redistribute it and/or modify
    it under the terms of the GNU General Public License as published by
    the Free Software Foundation, either version 3 of the License, or
    (at your option) any later version.

    This program is distributed in the hope that it will be useful,
    but WITHOUT ANY WARRANTY; without even the implied warranty of
    MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
    GNU General Public License for more details, in file LICENSE.md.


