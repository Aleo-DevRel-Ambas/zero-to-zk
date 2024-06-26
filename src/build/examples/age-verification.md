# Program for Age Verification using Zpass

In the following walkthrough steps we'll go through how one can code a leo program that is used to verify if someone is older than a certain age set as a boundary, for example older than 18. This is using the logic laid out in the zpass protocol and only includes the work the verifier has to do. In a real word scenario, there would have to be an `issuer` ( for example: government ) that signs the credential, for example `id card` provided by the client/user so then the `verifier` can verify that the credential provided was issued by a trusted authority and then can continue to verify facts about the user based on the information in the credential.

Note, that this is all done in zero knowledge, which means the verifier never actually gets to know the user's date of birth. Rather they code in logic into the verification program to check if the user's age is greater than 18, thus they only have to verify that the computation was done correctly and that the output was true.

You can try a demo out at :
TODO: Upload demo, Insert link

## Main Components

### Credential
A credential is a data structure that holds all data fields required for verification, for example it can represent a digital counterpart to our physical id cards.

The credential we use for this example has the following attributes:

| Attribute | Definition |
|---|---|
| Issuer | Issuing authority public address |
| | ex. Aleo Address of the credential Issuer. |
| Subject | Holder public address |
| | ex. Aleo Address of the credential Holder. |
| Date of Birth | Attribute value represented as a field. |
| Nationality | Attribute value encoded from a string and represented as a field. |
| Expiration | Attribute value represented as a field. |

>**Note**: The `Issuer` , `Subject` and `Expiration` are always required.

### Verification
To verify a credential one must verify that the credential was actually signed by the trusted issuer. And moreover, then one must verify the fact they are proving for so for example in this case is the user's age greater than the age treshold set.

### Helper Functions
The `timestamp_to_datetime` and `datetime_to_timestamp` are just auxiluary functions in relation to the `datetime` struct so that a user can input a unix timestamp representing the date of birth and it will be converted into a date time that we can use to calculate the user's age against the current time.

>**Warning**: This example is for demonstration purposes and is not intended to be used in a production environment.

## Program

```
program age_verification.aleo{

    //timestamp helper functions
    struct date_time {
        seconds: u8,        // Seconds in the time
        minutes: u8,        // Minutes in the time
        hours: u8,          // Hours in the time
        day: u8,            // Day of the month
        month: u32,          // Month of the year
        year: u32,          // Year
    }

    function timestamp_to_datetime(t: u32) -> date_time {
        let seconds: u32 = t % 60u32;
        t /= 60u32;

        let minutes: u32 = t % 60u32;
        t /= 60u32;

        let hours: u32 = t % 24u32;
        t /= 24u32;

        let a: u32 = (4u32 * t + 102032u32) / 146097u32 + 15u32;
        let b: u32 = t + 2442113u32 + a - (a / 4u32);
        let c: u32 = (20u32 * b - 2442u32) / 7305u32;
        let d: u32 = b - 365u32 * c - (c / 4u32);
        let e: u32 = d * 1000u32 / 30601u32;
        let f: u32 = d - e * 30u32 - e * 601u32 / 1000u32;
        let g: u32 = 4716u32;
        let h: u32 = 1u32;

        if (e > 13u32) {
            g = 4715u32;
            h = 13u32;
        }

        c -= g;
        e -= h;

        let dt: date_time = date_time {
            seconds: seconds as u8,
            minutes: minutes as u8,
            hours: hours as u8,
            day: f as u8,
            month: e as u32,
            year: c as u32,
        };

        return dt;
    }

    function datetime_to_timestamp(dt: date_time) -> u32 {
        let y: u32 = dt.year as u32;
        let m: u32 = dt.month as u32;
        let d: u32 = dt.day as u32;

        if (m <= 2u32) {
            m += 12u32;
            y -= 1u32;
        }

        let t: u32 = (365u32 * y) + (y / 4u32) - (y / 100u32) + (y / 400u32);
        t += (30u32 * m) + (3u32 * (m + 1u32) / 5u32) + d;
        t -= 719561u32;
        t *= 86400u32;
        t += (3600u32 * dt.hours as u32) + (60u32 * dt.minutes as u32) + dt.seconds as u32;

        return t;
    }

     struct Credentials {
        issuer: address,
        subject: address,
        dob: u32,
        nationality: field,
        expiry: u32
    }

    inline get_psd_hash(msg: Credentials) -> field {
        return Poseidon2::hash_to_field(msg);
    }

    inline get_bhp_hash(msg: Credentials) -> field {
        return BHP1024::hash_to_field(msg);
    }


    function get_hash(hash_type: u8, msg: Credentials) -> field {
        if (hash_type.eq(1u8)) {
            return get_bhp_hash(msg);
        }
        return get_psd_hash(msg);
    }


    function signature_verification(msg_hash: field, sig: signature, issuer: address) -> bool {
        return signature::verify(sig, issuer, msg_hash);
    }

    transition verify(
        sig: signature,
        hash_type: u8,
        currentDate: u32,
        msg: Credentials,
    ) -> bool {
        let dob: date_time = timestamp_to_datetime(msg.dob);
        let current: date_time = timestamp_to_datetime(currentDate);
        let age: u32 = current.year - dob.year;

        return ( age.gte(18u32) &&
            signature_verification(get_hash(hash_type, msg), sig, msg.issuer));
    }

}
```