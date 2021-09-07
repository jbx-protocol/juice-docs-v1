---
description: 'Juicebox is composed of the following contracts:'
---

# Contracts



<table>
  <thead>
    <tr>
      <th style="text-align:left">Contract</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b><code>TerminalV2PaymentLayer</code></b>
      </td>
      <td style="text-align:left">
        <p>Manages all inflows and outflows of funds into the Juicebox ecosystem.
          This contract also stores all treasury funds for all projects.</p>
        <p></p>
        <p><a href="terminalv2paymentlayer/">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>TerminalV2DataLayer</code></b>
      </td>
      <td style="text-align:left">
        <p>Stitches together funding cycles and treasury tokens, making sure all
          activity is accounted for and correct.</p>
        <p></p>
        <p><a href="terminalv2datalayer/">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>TerminalDirectory</code></b>
      </td>
      <td style="text-align:left">
        <p>Keeps a reference of which terminal contract each project is currently
          accepting funds through. When a project migrates from one terminal to another,
          the change is reflected in this contract.</p>
        <p></p>
        <p><a href="terminaldirectory/">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>Projects</code></b>
      </td>
      <td style="text-align:left">
        <p>Manages ownership over projects, which are represented as ERC-721 tokens.</p>
        <p></p>
        <p><a href="projects.md">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>TicketBooth</code></b>
      </td>
      <td style="text-align:left">
        <p>Manages token minting and burning for all projects, while keeping track
          of staked/unstaked and locked/unlock token balances for all accounts.</p>
        <p></p>
        <p><a href="ticketbooth/">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>FundingCycles</code></b>
      </td>
      <td style="text-align:left">
        <p>Manage funding cycle configurations, ballots, accounting, and scheduling.</p>
        <p></p>
        <p><a href="fundingcycles/">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>SplitStore</code></b>
      </td>
      <td style="text-align:left">
        <p>Stores splits information for all groups of each project. Projects can
          create split groups for directing percents of a total token allocation
          to any address, any other Juicebox project, or any contract that inherits
          from the ISplitAllocator interface.</p>
        <p></p>
        <p><a href="splitstore/">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>OperatorStore</code></b>
      </td>
      <td style="text-align:left">
        <p>Stores operator permissions for all addresses. Addresses can give permissions
          to any other address to take specific actions throughout the Juicebox ecosystem
          on their behalf.</p>
        <p></p>
        <p><a href="operatorstore/">more</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>Prices</code></b>
      </td>
      <td style="text-align:left">
        <p>Manage and normalizes ETH price feeds.</p>
        <p></p>
        <p><a href="prices/">more</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

A full architecture diagram showing the paths through which each contract's transactions can be called is found [here](https://www.figma.com/file/YIf64bRfSXjCDSPb49uAwv/Juicebox-Technical-Docs-Copy?node-id=262%3A8).

![](../../.gitbook/assets/architecture%20%282%29.png)

