---
layout: post
title: Kaleidoscope Whiteboard
date: 2017-01-19
---

# IOHK | Kaleidoscope Whiteboard with Bernardo David

## Online Poker

- trust third party entity
- reliance on regulators
- reliance of honest companies

## Research

- 1980s: Shamir, Rivest, Adleman
- inventors of RSA public encryption algorithm
- protocol that guarantees players cannot cheat each other

## Problems

- even if poker game is secure, without leaking strategy, proof of winning, what guarantee winner recieves payment
- protocol can terminate or abort abruptly
- rage quit
- :( Abort
- :( Rewards

## Kaleidoscope

- first formal securtiy definition for poker protocols
- provable secure protocol
- 2014 research IEEE conference
	+ Andrychowicz et al.
- rewards that are guarantees
- winner assured to recieve payment
- penalty enforcement
- efficient

## Simulation Definition

- capture the whole flow of the game
	+ uninterested in the minutia of game hands
- how to achieve reward **guarantees**
	+ **cryptocurrencies**
		* deposit some **collateral**
		* used to punish if they cheat
			- can be an abort
		* completion will deliver back
		* bet **deposits**
- how to achieve **efficiency**
	+ DDH assumption
	+ ROM random oracle model
- when someone is caught cheating the system
	+ players report to the smart contract
	+ provide proofs of the adversary's wrong doing
	+ collateral potentially redistributed to the honest players
	+ we need compact witnesses for proofs
