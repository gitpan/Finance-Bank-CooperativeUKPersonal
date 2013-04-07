NAME
    Finance::Bank::CooperativeUKPersonal - Access to UK Cooperative personal
    bank accounts

SYNOPSIS
      use Finance::Bank::CooperativeUKPersonal

      my $conf = {
            sortCode => $sortCode,            # numeric, without dashes,
            accountNumber => $accountNumber, 
            securityCode => $securityCode,
            memorableDate => $memorableDate,  # dd/mm/yyyy
            memorableName => $memorableName,
            firstSchool => $firstSchool,
            lastSchool => $lastSchool,
            birthPlace => $birthplace
      };
  
      # Connect to bank & login
      my $bank = Finance::Bank::CooperativeUKPersonal->new($conf);
      $bank->connect();
  
      # get account summary (all accounts table)
      my $accounts = $bank->accountSummary();
  
      # get available statements for an account
      # includes dated statements & recent items statement
      my $statements = $bank->availableStatements( $accounts->[0] );

      # fetch a statement - dated or recent items
      my $transactions = $bank->statement( $statements->[0] );

DESCRIPTION
    This module provides an interface to the Co-operative UK personal online
    banking website, with access to recent transactions and statements.

METHODS
  new, connect - combine to start your session
      my $conf = {
            sortCode => $sortCode,            # numeric, without dashes,
            accountNumber => $accountNumber, 
            securityCode => $securityCode,
            memorableDate => $memorableDate,  # dd/mm/yyyy
            memorableName => $memorableName,
            firstSchool => $firstSchool,
            lastSchool => $lastSchool,
            birthPlace => $birthplace
      };
  
      # Connect to bank & login
      my $bank = Finance::Bank::CooperativeUKPersonal->new($conf);
      $bank->connect();

    "new()" configures your connection, "connect()" connects to the co-op
    website. The co-op expires sessions after 10 minutes of inactivity, so
    "connect()" can be used at any point to reconnect.

  accountSummary - summary listing of bank accounts
    Parses the initial account summary table shown after logging into the
    Co-Op bank website. Returns a reference to an array of account hashrefs.
    e.g.

      $accounts = $bank->accountSummary();

    returns

      $accounts = [
        {
            'accountNumber' => $accountNumber,
            'sortCode' => $sortCode,
            'href' => $href,
            'name' => $name,
            'availableBalance' => $balance
        }
      ];

    If your balance is in credit, the co-op appends 'CR' to the available
    balance. I do not know how they indicate overdrawn accounts.

  availableStatements - list of availalable statements for an account
    Returns a list of available statements for an account as a reference to
    an array of hashrefs.

      $statements = $bank->availableStatements($accounts->[0]);

    returns

      $statements = [
        {
            'name' => $name, 
            'href' => $href
        }
      ];

    In the returned hashref, 'name' is either 'Recent Items' or the
    statement's issued date in dd/mm/yyyy format.

  statement - list of transaction rows in a statement
    Returns a list of transactions in a statement as a reference to an array
    of hashrefs.

      $transactions = $bank->statement($statements->[1]);

    returns

      $transactions = [
            {
            'reference' => $referenceString,
            'date' => $date,  # 'dd/mm/yyyy',
            'withdrawal' => $withdrawal
            'deposit' => $deposit,
            'balance' => $balance  # not present in recent items statement
         },
      ];

    If your balance is in credit, the co-op appends 'CR' to the available
    balance. I do not know how they indicate overdrawn accounts.

CAVEATS
    As this module accesses your bank account, you should ensure you store
    your configuration data and any received transaction information in a
    secure place.

AUTHOR
    Stephen Patterson <steve@patter.me.uk>

