# TitleSense Product Spec — The Interpretation Layer

**Status:** Active build sheet. **Owner:** TitleSense.
**Relabeled 2026-08-04 per D-001.** This document was originally written from the DeedPro side, before the two-product split existed, and its original title and closing section implied DeedPro would build the interpretation layer. It won't. The product spec below — Ownership Timeline, Loan & Lien Tracker, Items Needing Review, document families, plain-English definitions, cautious status language, confidence levels — is **TitleSense's**, and this is where it lives.

Only the framing has changed. The body is preserved as written, including the worked chain-of-title example, because it is the clearest statement of the product that exists.

**Scope note:** everything here is **v2** (the TitlePoint chain layer). The v1 prelim product — prelim in, plain-English summary out, PCT as first customer — is sequenced ahead of it per D-002 and is not described in this document.

---

## Executive Summary

We successfully captured and organized the TitlePoint Title Searching documentation into a structured reference system that can now be queried in plain English.

Instead of manually digging through more than 50 documentation pages, we created a searchable knowledge package containing:

- TitlePoint search workflows
- SOAP methods
- Service types
- Input parameters
- Result-retrieval methods
- Title plant differences
- Property, name, document, tax, starter, and legal-and-vesting searches
- State and county-specific legal formats
- Deprecated method warnings
- Source URLs for traceability

This gives us a practical foundation for understanding how TitlePoint searches work and, more importantly, how TitleSense transforms raw title-search results into something a homeowner can actually understand.

The larger opportunity is not simply retrieving a chain of title.

The opportunity is to turn complex recorded property history into a clear story about:

- Who owned the property
- When ownership changed
- Which loans were recorded
- Which loans appear to have been released
- Which liens or encumbrances may still matter
- Which items require professional review

---

## What We Did

### 1. Captured the Title Searching Documentation

The TitlePoint documentation site is JavaScript-driven, so saving the main webpage only captured the shell of the documentation.

We used the browser console to automatically retrieve all pages under the **Title Searching** section.

The capture included:

- 53 documentation pages
- 53 successful responses
- TitlePoint, TPX, and Hybrid search documentation
- Search examples
- Create-service methods
- Result-retrieval methods
- Parameter definitions
- Legal formats
- Plant and county reference tables

### 2. Converted the Documentation Into a Reference Package

We transformed the raw HTML into a package that can be searched and referenced more reliably.

The reference package includes:

- A cleaned Markdown reference
- Retrieval-ready JSONL chunks
- A structured inventory of methods, service types, and parameters
- Source filenames and URLs attached to each chunk

The package currently includes:

- 116 searchable semantic chunks
- 19 identified API methods
- 32 identified service types
- 162 identified parameters

This allows us to ask human questions such as:

> What is the correct workflow for submitting a property search?

> How do we retrieve the result after calling CreateService3?

> Which service type should we use for a property search?

> What does Property.IncludeUnderlying do?

> Can TitlePoint return a deed chain?

> Which methods are deprecated?

---

## What the TitlePoint Workflow Looks Like

TitlePoint searches are asynchronous.

The documented workflow is:

1. Submit the search using a create-service method.
2. Receive a Request ID and Order ID.
3. Use the Request ID to call `GetRequestSummaries`.
4. Continue checking until the request reaches a completed status.
5. Read the result IDs returned in the request summary.
6. Retrieve each result using the appropriate result method.

A simplified version looks like this:

```text
CreateService3 or CreateService4
        ↓
Request ID + Order ID
        ↓
GetRequestSummaries
        ↓
Wait for Complete status
        ↓
Result ID
        ↓
GetResultByID3
```

This matters because a single search request may produce more than one search result.

For example, an address search could trigger:

- An address lookup
- A property search
- A tax search
- A related parcel search

The system must understand the request summary before assuming there is only one result.

---

## The Chain-of-Title Opportunity

TitlePoint can return property document history and supports result filters designed around deed chains and open encumbrances.

Relevant result filters include:

- `Deedchain`
- `DeedchainOnly`
- `OpenEncumbrances`
- `OpenEncumbrancesOnly`

The documented deed-chain filter is intended to return conveyance documents back to the last qualifying full-value deed.

This creates the core opportunity for TitleSense.

Instead of showing a homeowner a raw list of recorded documents, we can interpret and organize those records into a plain-English property history.

---

# Explicit Chain-of-Title Example

## What the Raw Data Might Look Like

A traditional title search result may look like this:

```text
03/14/2012 – Grant Deed
03/14/2012 – Deed of Trust
08/22/2017 – Assignment of Deed of Trust
06/10/2021 – Reconveyance
11/04/2023 – Grant Deed
11/04/2023 – Deed of Trust
```

To a title professional, this may be familiar.

To a homeowner, it is mostly noise.

The homeowner may not understand:

- Which documents changed ownership
- Which documents represent loans
- Why an assignment appears
- Whether a reconveyance means a loan was paid
- Whether the old lender still has an interest
- Whether the newest loan is still active

---

## The Same Chain as a Homeowner Story

# Your Home's Ownership Story

## March 14, 2012 — The Property Changed Hands

A grant deed was recorded transferring the property from Robert Jones to Michael Brown.

This document generally represents a change in recorded ownership.

## March 14, 2012 — A Home Loan Was Recorded

A deed of trust was recorded on the same day as the ownership transfer.

This usually means Michael Brown financed the purchase and the lender recorded a security interest against the property.

## August 22, 2017 — The Loan Was Transferred

An assignment of deed of trust was recorded.

This usually means the lender's interest in the loan was transferred to another financial institution.

This did not necessarily change ownership of the property.

## June 10, 2021 — The Earlier Loan Appears to Have Been Released

A reconveyance was recorded that appears related to the 2012 deed of trust.

A reconveyance commonly indicates that a previous deed of trust was paid off or otherwise released.

## November 4, 2023 — Ownership Changed Again

A grant deed was recorded transferring the property from Michael Brown to Jerry Smith.

This appears to be the current recorded ownership transfer.

## November 4, 2023 — A New Loan Was Recorded

A new deed of trust was recorded shortly after the 2023 ownership transfer.

This likely represents financing connected to the current owner's purchase.

No matching release has been identified for this newer deed of trust, so it may still be active.

---

## Visual Ownership Timeline

```text
Robert Jones
     ↓ Grant Deed — 03/14/2012
Michael Brown
     ↓ Grant Deed — 11/04/2023
Jerry Smith
```

## Loan Timeline

```text
2012 Deed of Trust
        ↓
2017 Assignment
        ↓
2021 Reconveyance
        ↓
Appears Released
```

```text
2023 Deed of Trust
        ↓
No Matching Release Identified
        ↓
Potentially Active
```

---

## The One-Minute Homeowner Summary

A homeowner-facing summary could say:

> The property changed ownership twice during the period reviewed. One earlier loan appears to have been released. A newer deed of trust was recorded with the most recent purchase and may still be active. No conclusion should be made about title status without professional review.

This gives the homeowner the answer they actually care about without forcing them to interpret six separate documents.

---

# How TitleSense Makes the Result Easier to Understand

## 1. Separate Ownership From Loans and Claims

A major source of confusion is that homeowners often assume every recorded document changes ownership.

The interface should create two clear tracks.

### Ownership History

This section would include:

- Grant deeds
- Quitclaim deeds
- Trustee's deeds
- Court-ordered transfers
- Transfers into or out of trusts
- Other conveyance documents

### Loans and Claims

This section would include:

- Deeds of trust
- Mortgages
- Assignments
- Reconveyances
- Releases
- Liens
- Notices of default
- Subordinations

This separation immediately makes the chain easier to understand.

---

## 2. Group Related Documents Into Families

Documents should not be displayed as unrelated events when they are clearly part of the same transaction.

For example:

```text
Loan Opened
Deed of Trust — 03/14/2012
        ↓
Loan Transferred
Assignment — 08/22/2017
        ↓
Loan Released
Reconveyance — 06/10/2021
```

TitleSense could label the group:

> 2012 Loan — Appears Released

Another loan family could say:

> 2023 Loan — No Matching Release Identified

This turns three technical recordings into one understandable story.

---

## 3. Add Plain-English Definitions

Every document type should include a short explanation.

### Grant Deed

This document generally records a transfer of ownership from one party to another.

### Quitclaim Deed

This document transfers whatever interest the signer may have had. It does not necessarily confirm the extent of that interest.

### Deed of Trust

This document usually represents a loan secured by the property.

### Assignment of Deed of Trust

This usually means the loan or lender's interest was transferred. It does not normally change property ownership.

### Reconveyance

This commonly indicates that a previously recorded deed of trust was paid or otherwise released.

### Notice of Default

This indicates that a lender recorded a formal notice related to missed loan obligations. It does not itself mean the property was sold.

### Trustee's Deed

This may indicate that ownership changed through a foreclosure-related process.

---

## 4. Use Cautious Status Language

TitleSense should avoid making legal or title conclusions from document matching alone.

Use language such as:

- Appears released
- Likely related
- May still affect the property
- No matching release was identified
- Recorded ownership indicates
- Professional review recommended

Avoid language such as:

- The title is clear
- The loan is definitely paid
- This lien is invalid
- You legally own the property
- There are no title problems

The product should explain the records, not pretend to replace a title examination.

---

## 5. Add Confidence Levels

Document relationships will not always be certain.

A useful confidence system could include:

### Confirmed Match

The release or assignment directly references the original recording.

### Likely Match

The parties, lender, recording sequence, amount, and timing strongly align.

### Possible Match

Some information aligns, but a direct reference is missing.

### No Matching Release Found

No related release was identified in the records reviewed.

This distinction adds transparency and prevents the interface from overstating certainty.

---

# Product Features This Opens Up

## 1. Ownership Timeline

A visual timeline showing:

- Previous owners
- Current recorded owner
- Dates of transfer
- Transfer document type
- Trust-related changes
- Foreclosure-related changes

This could be the main homeowner view.

---

## 2. Loan and Lien Tracker

A dedicated section showing:

- Each recorded deed of trust
- Assignments connected to it
- Reconveyances or releases
- Possible active encumbrances
- Items without a matching release

This could become one of the most valuable homeowner features.

---

## 3. “Tell Me the Story” Mode

Instead of showing tables, TitleSense could generate a short narrative:

> The property was transferred to Michael Brown in 2012. A loan was recorded at the time of purchase. That loan was later assigned and appears to have been released in 2021. Ownership transferred again in 2023, when a new deed of trust was recorded.

This is ideal for non-technical users.

---

## 4. “What Changed?” Mode

A homeowner could compare two dates.

Example:

> Since you purchased the property, one new deed of trust and one lien have been recorded.

This could be especially useful for:

- Fraud monitoring
- Post-closing review
- Estate planning
- Divorce
- Trust changes
- Home equity lending

---

## 5. “Why Is This Name Here?” Explanations

A homeowner could click a person or company name.

Example:

> Wells Fargo appears as the beneficiary of a deed of trust. This does not mean Wells Fargo owned the property.

Another example:

> ABC Mortgage Services appears as an assignee. This usually means the lender's interest was transferred.

---

## 6. Open-Item Review

The system could automatically surface:

- Deeds of trust without a matching reconveyance
- Liens without a matching release
- Notices of default without a later rescission or trustee's deed
- Ownership transfers that do not clearly connect
- Conflicting owner names
- Unexpected trust or entity transfers

The interface should label these as:

> Items worth reviewing

Not:

> Defects

---

## 7. Trust and Estate Explanations

Ownership chains often become confusing when a property moves between an individual and a trust.

TitleSense could explain:

```text
Jerry Smith
     ↓ Transfer Into Trust
Jerry Smith, Trustee of the Smith Family Trust
```

Plain-English explanation:

> The recorded owner changed from Jerry Smith individually to Jerry Smith as trustee of the Smith Family Trust. This may represent an estate-planning transfer rather than a sale.

---

## 8. Foreclosure Storytelling

A foreclosure-related chain could be grouped as:

```text
Deed of Trust
     ↓
Notice of Default
     ↓
Notice of Trustee's Sale
     ↓
Trustee's Deed
```

The homeowner explanation could distinguish:

- Loan default
- Scheduled sale
- Completed ownership transfer

That distinction is critical because a notice of default does not itself mean foreclosure was completed.

---

## 9. Fraud and Ownership Monitoring

Once document relationships are normalized, TitleSense could identify newly recorded activity such as:

- Unexpected deeds
- Unexpected liens
- New deeds of trust
- Ownership changes
- Trust transfers
- Assignments
- Foreclosure notices

This opens the door to proactive property monitoring.

---

## 10. Guided Homeowner Questions

The homeowner could ask:

- Who owned this property before me?
- Is the old mortgage still showing?
- Why is this lender listed?
- What does this reconveyance mean?
- Was this property ever in foreclosure?
- Why is a trust listed as the owner?
- Are there liens without releases?
- Did this document change ownership?
- What happened on the day I purchased the property?

This is where the indexed TitlePoint documentation becomes especially valuable.

We can use the documentation to understand how searches and filters work, then use the returned property data to answer those questions in plain English.

---

# Recommended First Prototype

The first version should focus on three outputs.

## 1. Ownership Timeline

Show who transferred the property to whom and when.

## 2. Loan and Lien Tracker

Group deeds of trust, assignments, reconveyances, liens, and releases.

## 3. Items Needing Review

Surface records where the relationship or current status is unclear.

This is focused enough to build and test without attempting to recreate a full title-production platform.

---

# Important Limitation

The TitlePoint documentation explains how to submit searches and retrieve results.

It does not, by itself, provide all of the business rules needed to determine legal title status.

TitleSense still needs:

- Document-type classification rules
- Recording-reference matching
- Party-name normalization
- Lender and assignee matching
- Recording-date logic
- County-specific behavior
- Human title-examiner validation
- Clear legal disclaimers

The product should present itself as an explanation and review tool, not as a substitute for a title policy, title report, or professional examination.

---

# Strategic Takeaway

The initial task was to index TitlePoint's HTML documentation.

What we actually created is the foundation for a much larger product capability.

TitlePoint provides the raw search and recorded-document data.

TitleSense provides the missing layer:

> A clear, visual, homeowner-friendly explanation of ownership, loans, liens, releases, and events requiring professional review.

The defensible value is not access to records. It is the interpretation layer that turns those records into a useful and trustworthy story.

DeedPro consumes that layer's output as violet proposals under `docs/H1_CONTRACT.md`, and builds no interpretation of its own. Two products, one seam.

---

## Changelog

- **2026-08-04** — relabeled from *DeedPro_TitlePoint_Chain_of_Title_Opportunity.md* to the TitleSense product spec per D-001. Framing, title, and closing section reassigned to TitleSense; body preserved. Scope marked v2 per D-002.
