---
description: >-
  The following terms are used frequently to describe ideas and mechanisms
  within the Juicebox protocol.
---

# Terminology

<table>
  <thead>
    <tr>
      <th style="text-align:left">Term</th>
      <th style="text-align:left">Definition</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b>Project</b>
      </td>
      <td style="text-align:left">Each Juicebox project is represented as an NFT (ERC-721) who&apos;s owner
        is the address that makes the <code>launchProject</code> transaction on the <code>TerminalV2DataLayer</code> contract.
        <br
        />
        <br />Ownership over this NFT permits admin-level interactions with the project.
        Like any other NFT, ownership can be transferred from the original owner
        to any other address, such as a multi-sig wallet.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Funding cycle </b>
      </td>
      <td style="text-align:left">A project&apos;s lifecycle is expressed in terms of funding cycles. A
        funding cycle is a data structure outlining the time-locked rules according
        to which a project wishes to operate.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Treasury tokens</b>
      </td>
      <td style="text-align:left">
        <p>The Juicebox protocol keeps track of treasury tokens for each project.
          When a payment is made to a project, the protocol mints tokens for a specified
          beneficiary according to the rules of the current funding cycle.</p>
        <p></p>
        <p>Projects can optionally call the <code>issue</code> transaction of the <code>TicketBooth</code> contract
          to issue ERC-20 tokens to represent their treasury tokens. Once issued,
          anyone with a project&apos;s treasury tokens can unstake them from the
          protocol&apos;s internal accounting mechanism into their wallet to use
          around Web3.</p>
        <p></p>
        <p>Treasury tokens can also be called Community tokens, or they may be referred
          to simply as a project&apos;s tokens.</p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Splits</b>
      </td>
      <td style="text-align:left">A Split is a data structure used to send a percent of a total amount to
        a preprogrammed address, Juicebox project, or contract that inherits from <code>ISplitAllocator</code>.
        A split does not hold information about what is being split, it&apos;s
        simply a group-able structure that maps a receiver to a percentage.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Overflow</b>
      </td>
      <td style="text-align:left">A funding cycle can have a <code>target</code> property which denotes how
        much funds a project can pull from its treasury during the cycle to distribute
        to its preprogrammed payout splits.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Discount rate</b>
      </td>
      <td style="text-align:left">Each funding cycle has a <code>weight</code>that is derived from multiplying
        the <code>weight</code> of the previous funding cycle by the <code>discountRate</code>of
        the previous cycle.
        <br />
        <br />The <code>weight</code> property can then be used to determine how many
        tokens are issued per unit of payment received during the funding cycle,
        or for any other functionality you wish to implement through your funding
        cycle&apos;s data sources and delegates.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Redemption rate</b>
      </td>
      <td style="text-align:left">Each funding cycle has a <code>redemptionRate</code> property that can be
        used to determine how much overflowed funds can be claimed by redeeming/burning
        treasury tokens, or for any other functionality you with to implement through
        your funding cycle&apos;s data sources and delegates.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Reserved tokens</b>
      </td>
      <td style="text-align:left">Each funding cycle has a <code>reservedRate</code>, which specifies the
        percentage of tokens minted as a result of newly received payments that
        should be reserved for distribution to a preprogrammed group of reserved
        token splits.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>Operator</b>
      </td>
      <td style="text-align:left">Addresses can give permissions to any other address to take specific actions
        throughout the Juicebox ecosystem on their behalf. These addresses are
        called Operators.</td>
    </tr>
  </tbody>
</table>



