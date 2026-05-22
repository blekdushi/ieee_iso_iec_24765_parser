# ieee_iso_iec_24765_parser

## About

The "Software and Systems Engineering Vocabulary" as defined in IEEE/ISO/IEC 24765:2017 is officially published and available in two formats:
- PDF (e.g. https://www.iso.org/standard/71952.html, https://ieeexplore.ieee.org/document/8016712)
- Online database with simple search form (https://sevocab.computer.org/)

The lack of a machine-readable dictionary complicates referencing the 'Software and Systems Engineering Vocabulary' within computer programs.

This repository features a Python script that uses pdfplumber to parse the 'Software and Systems Engineering Vocabulary' PDF into JSON format.

## Analysis of the PDF formatting and structure

Example for a term:

> **3.51**
>
> **acquirer**
>
> owner
>
> purchaser
>
> **1\.** stakeholder that acquires or procures a product or service from a supplier *[ISO/IEC 12207:2008 Systems and
> software engineering — Software life cycle processes, 4.1; ISO/IEC TS 24748-1:2016 Systems and software engineering
> — Life cycle management — Part 1: Guide for life cycle management, 2.1; ISO/IEC/IEEE 15288:2015 Systems and
> software engineering — System life cycle processes, 4.1.1]* 
> **2\.** person or organization that acquires or procures a
> system, software product, or software service (which can be part of a system) from a supplier *[ISO/IEC TR
> 12182:2015 Systems and software engineering — Framework for categorization of IT systems and software, and guide
> for applying it, 3.13]*
> **3\.** individual or organization that acquires or procures a system, software product or software
> service from a supplier *[ISO/IEC 25040:2011 Systems and software engineering — Systems and software Quality
> Requirements and Evaluation (SQuaRE) — Evaluation process, 4.1]*
>
> Note 1 to entry: The acquirer can be internal or external to the supplier organization. Acquisition of a software 
> product can involve, but does not necessarily require, a legal contract or a financial transaction between 
> the acquirer and supplier.


Each entry begins with a term number in bold font. This is a decimal number starting with '3.', followed by one to four sequential digits that increment by 1 for each subsequent term (e.g., **3.51**).

The term name follows immediately after the number and is also formatted in bold (e.g., **acquirer**).

Following the name, there may be zero or more term synonyms, which are printed in a regular (non-bold) font (e.g., owner).

Each term contains one or more descriptions. Each description is introduced by a bold ordinal number, starting at "1.". A description consists of descriptive text and may include italicized references to ISO/IEC/IEEE standards enclosed in square brackets (e.g., *[ISO/IEC 25040:2011 Systems and software engineering — Systems and software Quality Requirements and Evaluation (SQuaRE) — Evaluation process, 4.1]*), indicating the exact context in which the term is used.

Finally, a term may include an optional note, which begins with the prefix 'Note 1 to entry:' followed by the note text. Although this syntax implies the possibility of multiple notes (e.g., 'Note 2 to entry'), no terms in the document actually contain more than one.

## Challenges & Limitations

Due to PDF's nature newlines and linebreaks are no reliable way to identify or tokenize the text into meaningful semantic chunks. The visible representation in a PDF reader application looks fine to the human eye but differs substantially from the raw text extracted with libraries such as pdfplumber. The scripts therefore uses font changes as delimiters for tokenizing the raw text.

To detect a new term, the script validates that the number after the decimal point is exactly one greater than that of the previously processed term.

The script identifies the very first term by searching for the initial occurrence of '3.1'. This logic relies on the assumption that the string '3.1' does not appear anywhere in the document prior to the first term; otherwise, the initial parsing would fail.

Similarly, the boundary of the final term is determined by checking for a hardcoded 'Annex' marker. This works because the 'Annex' heading signals the start of a new chapter, effectively concluding the term listings.

The script can not parse images. They are omitted in the resulting JSON.