# Need of OMS
* Orders sync with oms.
* Then loyalty transactions are updated or created from orders based on rules defined in the salesforce org(flows).
* Loyalty enrollment and redemptions(Points/Vouchers), loyalty profiles is synced with sfcc storefront through cartridges.
![[loyalty-cc-integration-design.png]]


# How to Earn Loyalty Points
* create tier groups and create tier and tier criteria in the tier group
* Create currency(points)
	* Qualifying currency(Activity based model- tier points)
	* Non qualifying currency(Fixed model- converted direrctly to traditional currency system)
* Add benefit type
* Assign benefits to tiers
* Create voucher definitions
* Create promotion to issue vouchers
* Create campaign to send emails about promotion to the targeted audience
* 