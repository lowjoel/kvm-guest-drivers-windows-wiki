# General

# Windows Server 2022

## **USB Generic HID Test** error "No MUTT devices were detected"

## **USB Type-C ACPI Validation** error

### Error text:
`AreEqual(g_numberOfTypeCPorts, expectedNumberOfTypeCPorts): Compare number of marked Type-C ports (_UPC in ACPI) to expected number of Type-C ports. Please make sure the parameter 'NumberOfUsbTypeCPorts' has been set correctly. If this is a USB Dual-Role system that uses the URS driver, connect a USB device to each Dual-Role capable connector and re-run the test. - Values (0, 3)`

### Test documentation: https://docs.microsoft.com/en-us/windows-hardware/test/hlk/testref/b3c41a3f-b844-4c2d-b115-dad51a37f123

### Solution
Set test job parameter _NumberOfUsbTypeCPorts_ to 0. 