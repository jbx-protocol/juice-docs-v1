# Contract APIs



<table>
  <thead>
    <tr>
      <th style="text-align:left">Contract</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b><code>JBProjects</code></b>
      </td>
      <td style="text-align:left">
        <p>Manages ownership over projects, which are represented as ERC-721 tokens.</p>
        <p></p>
        <p><a href="jbprojects/">View API</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>JBPaymentTerminal</code></b>
      </td>
      <td style="text-align:left">
        <p>Manages all inflows and outflows of funds into the Juicebox ecosystem.
          This contract also stores all treasury funds for all projects.</p>
        <p></p>
        <p><a href="jbpaymentterminal/">View API</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>JBPaymentTerminalData</code></b>
      </td>
      <td style="text-align:left">
        <p>Stitches together funding cycles and treasury tokens, making sure all
          activity is accounted for and correct.</p>
        <p></p>
        <p><a href="jbpaymentterminaldata/">view API</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>JBDirectory</code></b>
      </td>
      <td style="text-align:left">
        <p>Keeps a reference of which terminal contract each project is currently
          accepting funds through. When a project migrates from one terminal to another,
          the change is reflected in this contract.</p>
        <p></p>
        <p><a href="jbdirectory/">View API</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>JBTokenStore</code></b>
      </td>
      <td style="text-align:left">
        <p>Manages token minting and burning for all projects, while keeping track
          of staked/unstaked and locked/unlock token balances for all accounts.</p>
        <p></p>
        <p><a href="jbtokenstore/">View API</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>JBFundingCycleStore</code></b>
      </td>
      <td style="text-align:left">
        <p>Manage funding cycle configurations, ballots, accounting, and scheduling.</p>
        <p></p>
        <p><a href="jbfundingcyclestore/">View API</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>JBSplitStore</code></b>
      </td>
      <td style="text-align:left">
        <p>Stores splits information for all groups of each project. Projects can
          create split groups for directing percents of a total token allocation
          to any address, any other Juicebox project, or any contract that inherits
          from the IJBSplitAllocator interface.</p>
        <p></p>
        <p><a href="jbsplitstore/">View API</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>JBOperatorStore</code></b>
      </td>
      <td style="text-align:left">
        <p>Stores operator permissions for all addresses. Addresses can give permissions
          to any other address to take specific actions throughout the Juicebox ecosystem
          on their behalf.</p>
        <p></p>
        <p><a href="jboperatorstore/">View API</a>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b><code>JBPrices</code></b>
      </td>
      <td style="text-align:left">
        <p>Manage and normalizes ETH price feeds.</p>
        <p></p>
        <p><a href="jbprices/">View API</a>
        </p>
      </td>
    </tr>
  </tbody>
</table>

A full architecture diagram showing the paths through which each contract's transactions can be called is found [here](https://www.figma.com/file/YIf64bRfSXjCDSPb49uAwv/Juicebox-Technical-Docs-Copy?node-id=262%3A8).

![](../../.gitbook/assets/architecture%20%282%29.png)

